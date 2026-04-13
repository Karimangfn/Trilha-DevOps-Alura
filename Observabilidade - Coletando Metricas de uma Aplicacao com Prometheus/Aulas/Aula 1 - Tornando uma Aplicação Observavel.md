# Tornando uma Aplicação Observável

## Observabilidade e Monitoramento

- **Observabilidade**: Focada em externalizar o comportamento do sistema em tempo de execução para entender como ele se comporta diante do consumo do usuário.
- **Monitoramento**: Acompanhamento do estado do sistema através de eventos registrados, permitindo ações reativas (após o incidente) e proativas.
- **Métricas**: Indicadores de nível de serviço coletados em séries temporais, essenciais para medir performance, disponibilidade, saturação e erros.

## Cenário e Estrutura do Projeto

### Stack Tecnológica
- **Linguagem/Framework**: Java 1.8 com Spring Boot.
- **Banco de Dados**: MySQL 5.7.
- **Cache**: Redis.
- **Gerenciamento**: Maven (utilizado para build e gerenciamento de dependências).
- **Infraestrutura**: Docker e Docker Compose para subir os serviços (`redis-forum-api`, `mysql-forum-api`, `prometheus-forum-api` e `alertmanager-forum-api`).

### Fluxo da Aplicação
- O tráfego passa por um **Proxy Reverso** (Nginx) até a API.
- A API consulta o **Redis** (Cache Hit/Miss) antes de acessar o **MySQL**.
- O cliente sintético gera tráfego para simular usuários reais (sucessos e falhas).

---

## Instrumentação com Spring Boot Actuator

O **Actuator** expõe endpoints que permitem monitorar e interagir com a aplicação.

### Configuração
- Adição da dependência no `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
- Configuração no arquivo `application-prod.properties`:
  - `management.endpoint.health.show-details=always`: Exibe detalhes completos da saúde do sistema.
  - `management.endpoints.web.exposure.include=health,info,metrics`: Define quais endpoints estarão acessíveis via web.

### Principais Endpoints
- `/actuator/health`: Verifica o status da aplicação e suas dependências (DB, Disk, Redis).
- `/actuator/info`: Exibe informações gerais da aplicação (versão, nome).
- `/actuator/metrics`: Lista todas as métricas disponíveis da JVM (CPU, memória, threads, pool JDBC).

---

## Integração com Prometheus via Micrometer

Para que o Prometheus consiga ler as métricas do Actuator, elas precisam ser convertidas para um formato específico.

### Implementação do Micrometer
- Adição da dependência no `pom.xml`:
```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```
- Exposição do novo endpoint: `management.endpoints.web.exposure.include=...,prometheus`.

### Configurações Adicionais no application-prod.properties
- `management.metrics.enable.jvm=true`: Habilita métricas detalhadas da JVM.
- `management.metrics.export.prometheus.enabled=true`: Ativa a exportação no formato Prometheus.
- `management.metrics.tags.application=app-forum-api`: Adiciona tags para facilitar a filtragem.
- `management.metrics.distribution.sla.http.server.requests=50ms, 100ms, 200ms, 300ms, 500ms, 1s`: Configura buckets para monitoramento de SLA.

---

## Configuração de Alertas (Alertmanager & Slack)

Sistema reativo para notificar falhas críticas como quebras de SLO e erros de servidor.

### Regras de Alerta (`alert.rules`)
Arquivo YAML onde são definidas as expressões PromQL:
- **ERRO 500**: Dispara se a taxa de erro for superior a 1% no último minuto.
```yaml
- alert: 'ERRO 500'
  expr: (sum(rate(http_server_requests_seconds_count{job="app-forum-api", status="500", uri!="/actuator/prometheus"}[1m]))/sum(rate(http_server_requests_seconds_count{job="app-forum-api", uri!="/actuator/prometheus"}[1m]))) >= 0.01
  for: 1m
  labels:
    severity: critical
```
- **QUEBRA DE SLO**: Dispara se 90% das requisições demorarem mais de 500ms.
```yaml
- alert: 'QUEBRA DE SLO'
  expr: (histogram_quantile(0.90, sum(rate(http_server_requests_seconds_bucket{job="app-forum-api", uri!="/actuator/prometheus"}[1m])) by (le))) >= 0.5
  for: 1m
```

### Integração com Slack (`alertmanager.yml`)
Configuração do receptor e template de mensagem:
- `slack_api_url`: URL do Webhook gerada no Slack.
- `group_by`: Agrupa alertas por `app`, `env` ou `group` para evitar spam.

---

## Execução e Infraestrutura

### Docker Compose
Os serviços de monitoramento devem ser integrados à rede da aplicação:
- **Prometheus**: Monta o volume `./prometheus/alert.rules` e aponta para o Alertmanager.
- **Alertmanager**: Porta `9093`, configurado com o arquivo de rotas para o Slack.

### Ciclo de Operação
- **Build**: `mvn clean package`.
- **Subir Stack**: `docker-compose up`.
- **Silenciamento**: Uso da aba "Silence" no Alertmanager para manutenções programadas.
- **Dashboards**: Exportação de arquivos JSON no Grafana para backup ou importação de IDs da comunidade (ex: `11378`).

### Validação
- Endpoints de métricas: `http://localhost:8080/actuator/prometheus`.
- Interface Prometheus: `http://localhost:9090/alerts`.
- Canal Slack: Recebimento de mensagens [FIRING] e [RESOLVED].
