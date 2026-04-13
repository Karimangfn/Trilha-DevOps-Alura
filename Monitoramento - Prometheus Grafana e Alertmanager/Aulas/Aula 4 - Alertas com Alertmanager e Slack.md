# Alertas com Alertmanager e Slack

## Configuração de Regras de Alerta no Prometheus

- As regras são definidas no arquivo `alert.rules` dentro do diretório do Prometheus, utilizando a sintaxe **YAML**.
- O elemento `groups` organiza os alertas em grupos específicos (ex: `ops-forum-api`).
- **Estrutura de um Alerta**:
  - `alert`: Nome do alerta.
  - `expr`: Expressão **PromQL** que serve como gatilho (trigger).
  - `for`: Tempo que a condição deve persistir para o alerta ser disparado.
  - `labels`: Rótulos para identificar criticidade (`severity`), ambiente (`env`) e aplicação (`app`).
  - `annotations`: Informações detalhadas como `title`, `summary` e `description`.

## Definição de Alertas Práticos

### Quebra de SLO (Service Level Objective)
- Baseado na latência das requisições.
- Regra: Disparar se 90% das requisições demorarem 500ms ou mais por 1 minuto.
- `expr`: `(histogram_quantile(0.90, sum(rate(http_server_requests_seconds_bucket{uri!="/actuator/prometheus"}[1m])) by (le))) >= 0.5`

### Taxa de Erro 500
- Monitora a saúde da aplicação através do status HTTP.
- Regra: Alerta se a taxa de erros 500 for superior a 1% no último minuto.
- `expr`: `(sum(rate(http_server_requests_seconds_count{status="500", uri!="/actuator/prometheus"}[1m])) / sum(rate(http_server_requests_seconds_count{uri!="/actuator/prometheus"}[1m]))) >= 0.01`

## Integração com Slack e Alertmanager

### Configuração do Slack
- Criação de um workspace e canal (ex: `#forum-api`).
- Adição do App **Incoming Webhook** para gerar uma URL de integração.
- Teste de conectividade via terminal utilizando o comando `curl`.

### Configuração do Alertmanager
- Arquivo `alertmanager.yml`:
  - `global`: Define a `slack_api_url` com o token gerado.
  - `route`: Define como os alertas são agrupados (`group_by`) e para qual destinatário (`receiver`) serão enviados.
  - `receivers`: Configura os detalhes do canal do Slack e o template da mensagem (título e texto customizados).
- O Alertmanager permite silenciar alertas ativos via interface (porta `9093`) para que a equipe trabalhe na resolução sem notificações constantes.

## Gestão da Infraestrutura (Docker Compose)

- **Volumes**: Montagem do arquivo `alert.rules` no container do Prometheus e do `alertmanager.yml` no container do Alertmanager.
- **Dependências**: O Alertmanager deve estar na mesma rede (`monit`) que o Prometheus.
- Configuração do `prometheus.yml` para apontar o alvo do Alertmanager em `targets: ['alertmanager-forum-api:9093']`.

## Testes e Resposta a Incidentes

- **Simulação de Falha**: Parar containers de dependências (MySQL/Redis) para validar a transição dos alertas de `pending` para `firing`.
- **Status do Alerta**: 
  - `Firing`: Condição ativa e notificação enviada.
  - `Resolved`: Notificação enviada automaticamente quando o sistema volta à normalidade.
- **Silenciamento**: Uso de *matchers* no Alertmanager para agrupar e silenciar múltiplos alertas por aplicação ou ambiente durante uma manutenção.

## Boas Práticas e Reuso de Dashboards

- **Backup**: Exportar dashboards do Grafana em formato **JSON** para garantir a persistência das configurações.
- **Comunidade**: Importação de dashboards prontos (ex: Spring Boot Monitor - ID `11378`) via site oficial do Grafana para acelerar o monitoramento de JVM e métricas padrão.
