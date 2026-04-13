# Metricas Default e Personalizadas

## Analise de Metricas Default da JVM

- As métricas são externalizadas pelo **Micrometer** e consumidas no endpoint `/actuator/prometheus`.
- **Principais Metricas de Sistema**:
    - `jvm_memory_used_bytes`: Utilização de memória (áreas *heap* e *non-heap* como *Compressed Class Space* e *Code Cache*).
    - `logback_events_total`: Eventos registrados no log (*info, trace, warn, error, debug*).
    - `hikaricp_connections_active`: Monitoramento de conexões ativas com o banco de dados via pool Hikari.
    - `jvm_gc_pause_seconds`: Tempo de execução e pausas do *Garbage Collector* (causas como *Allocation Failure* ou *Metadata GC Threshold*).
    - `jvm_threads_states_threads`: Estado das threads (*waiting, runnable, timed-waiting, blocked*).
    - `process_cpu_usage` e `system_cpu_count`: Uso de CPU pelo processo e contagem de núcleos do sistema.
- O endpoint não é dinâmico; para visualizar dados atualizados, é necessário realizar uma nova consulta (refresh manual ou via *scrape time* do Prometheus).

## Monitoramento de SLA e Desempenho

- Uso da métrica `http_server_requests_seconds_bucket` para medir a experiência do usuário.
- **Buckets de Resposta**: Configurados via `application.properties` (50ms, 100ms, 200ms, 300ms, 500ms, 1s).
- Valores acima de 500ms ou 1s indicam problemas críticos de performance na API.
- A métrica mapeia automaticamente todos os endpoints (ex: `/topicos`, `/auth`) e status de retorno (200, 403, 500).

## Criacao de Metricas Personalizadas

- Focada em métricas que o Actuator não mapeia por padrão, como regras de negócio.
- **Implementacao no AutenticacaoController**:
    - Uso de atributos do tipo `Counter` para registrar sucessos e erros de login.
    - Injeção do `MeterRegistry` no construtor da classe para registro das métricas.
    - **Metodos Utilizados**:
        - `Counter.builder("auth_user_success").description("...").register(registry)`
        - `authUserSuccess.increment()`: Chamado dentro do bloco `try` após autenticação válida.
        - `authUserErrors.increment()`: Chamado dentro do bloco `catch` em caso de `AuthenticationException`.

## Testes e Validacao com Postman

- **Configuracao da Requisicao**:
    - Método: `POST` em `localhost:8080/auth`.
    - Headers: `Content-Type: application/json`.
    - Body: JSON com `email` e `senha`.
- **Fluxo de Teste**:
    - Envio de dados corretos gera `200 OK`, `token` e incrementa `auth_user_success_total`.
    - Envio de dados incorretos gera `400 Bad Request` e incrementa `auth_user_error_total`.
- As métricas de erro só aparecem no endpoint do Actuator após a primeira ocorrência do evento.

## Preparacao para Conteinerizacao

- Ajuste da aplicação para rodar na rede interna do Docker (Docker Bridge).
- **Alteracoes no application-prod.properties**:
    - `spring.redis.host=redis-forum-api`: Aponta para o nome do serviço do Redis no Docker Compose.
    - `spring.datasource.url=jdbc:mysql://mysql-forum-api:3306/forum`: Aponta para o container do MySQL.
- **Build do Artefato**:
    - Uso do comando `mvn clean package` no terminal para gerar o arquivo `.jar` com as novas configurações de rede.
    - O novo artefato deixa de buscar o banco em `localhost` para buscá-lo dentro da stack do Docker.
