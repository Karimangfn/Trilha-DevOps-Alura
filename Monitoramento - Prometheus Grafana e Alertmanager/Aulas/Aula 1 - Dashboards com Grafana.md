# Dashboards com Grafana

## Instalação e Configuração

- Criar um diretório chamado `grafana` no diretório de trabalho do Docker Compose.
- Alterar as permissões do diretório com `chmod 777 grafana` para garantir que o container consiga gravar dados no volume.
- No `docker-compose.yml`, configurar o serviço `grafana/grafana` mapeando o volume `./grafana:/var/lib/grafana` e a porta `3000`.
- Ajustar as dependências (`depends_on`) para que o Grafana inicie após o Prometheus, e o client após o Grafana.
- Acessar o Grafana via `localhost:3000` (credenciais padrão: `admin/admin`).
- Configurar o **Data Source** selecionando Prometheus e utilizando a URL do container: `http://prometheus-forum-api:9090`.
- Criar uma pasta (`Folder`) chamada `forum-api` para organizar os dashboards.

## Estrutura do Projeto

- **Dashboards e Organização**:
  - `dash-forum-api`: Dashboard principal criado para a aplicação.
  - **Rows**: Utilização de linhas como a `API Basic` para agrupar painéis logicamente.
- **Variáveis Dinâmicas**:
  - Uso de variáveis do tipo **Query** para tornar o monitoramento flexível a múltiplas instâncias.
  - `$application`: Definida através de `label_values(application)`.
  - `$instance`: Definida através de `label_values(jvm_classes_loaded_classes{application="$application"}, instance)`.
  - `$pool`: Definida através de `label_values(hikaricp_connections{instance="$instance", application="$application"}, pool)`.

## Desenvolvimento de Painéis

### Métricas de Processo
- **UPTIME**: Utiliza `process_uptime_seconds`. Formatação como `Time > duration (hh:mm:ss)` no modo de visualização **Stat**.
- **START TIME**: Utiliza `process_start_time_seconds * 1000`. Formatação como `Data & time > Datetime ISO` para identificar o momento da última inicialização.

### Análise de Logs (Logback)
- **Painel "WARN & ERROR LOG"**:
  - Utiliza a métrica `logback_events_total` filtrada pelos labels `$application`, `$instance` e `level`.
  - Aplicação da função `increase(...[5m])` para observar o volume de eventos nos últimos 5 minutos.
  - Uso da função `sum()` para agregação de valores.
  - Configuração de legenda via referência `{{level}}` ou nomes estáticos para identificar erros e avisos.
  - Customização visual: Cores amarelo para `warn` e vermelho para `error`, modo de gradiente `Opacity` e unidade `Misc > short`.

### Monitoramento de Banco de Dados (JDBC Pool)
- **Painel "JDBC POOL"**:
  - Utiliza a métrica `hikaricp_connections` com os labels de instância, aplicação e pool.
  - Visualização do tipo **Stat** focada no último valor (`Last`).
  - Configuração de **Thresholds**: Cor verde para valores saudáveis (ex: 10) e vermelho como base (alerta de queda).

## Metodologias e Boas Práticas

- **Golden Signals (Sinais de Ouro)**: Foco em Latência, Tráfego, Saturação e Erros (referência Google SRE).
- **Métodos RED e USE**:
  - **RED**: Rate (Taxa), Errors (Erros) e Duration (Duração). Focado na experiência do usuário e protocolos HTTP.
  - **USE**: Utilization (Utilização), Saturation (Saturação) e Errors (Erros). Focado em infraestrutura.
- **Benefícios do Monitoramento**:
  - Redução do **MTTD** (Tempo Médio de Detecção) e **MTTR** (Tempo Médio de Reparo).
  - Gestão de **SLA** (Acordo), **SLO** (Objetivo) e **SLI** (Indicador).
  - Identificação de **Error Budgets** e criação de **Baselines** comportamentais para escalabilidade preditiva.
- **Ações Reativas vs. Proativas**: Alertas e chamados automatizados (reativo) contra escalabilidade baseada em métricas (proativo).

## Testes e Validação
- Simulação de incidentes através da interrupção de containers (`docker container stop mysql-forum-api`) para validar a alimentação das métricas de erro e queda de conexões no Grafana.
- Reinicialização de serviços para observar a alteração dinâmica dos valores de Uptime e JDBC Pool.
