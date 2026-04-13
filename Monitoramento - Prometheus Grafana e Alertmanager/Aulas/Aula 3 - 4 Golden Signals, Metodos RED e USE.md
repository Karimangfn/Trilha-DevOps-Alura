# 4 Golden Signals, Metodos RED e USE

## Latency Average e Request Count

### Configuração de Latência (Quantiles)
- Utilização da métrica `http_server_requests_seconds_bucket` para calcular percentis de tempo de resposta.
- Aplicação da função `histogram_quantile()` para definir faixas como 99% (P99), 90%, 75%, 50% e 25%.
- Uso do seletor `uri!="/actuator/prometheus"` para filtrar métricas internas de monitoramento.
- Estrutura da query: `histogram_quantile(0.99, sum(rate(métrica[1m])) by (le))`.
- Configuração do painel: Visualização em **Time series**, unidade em **seconds (s)** e legenda em modo **Table**.



### Contagem de Requisições por Endpoint
- Uso da métrica `http_server_requests_seconds_count` com a função `increase()` para medir o crescimento no último minuto.
- Agregação com `sum()` para consolidar dados de múltiplos status (ex: sucessos e erros 500) em uma única linha por URI.
- Monitoramento de endpoints específicos como `/topicos`, `/topicos/{id}` e `/auth`.

## Duração de Requisições (RED)

### Average Request Duration
- Cálculo da média de duração dividindo a soma total de tempo pelo número total de requisições.
- Query: `rate(http_server_requests_seconds_sum[1m]) / rate(http_server_requests_seconds_count[1m])`.
- Foco em requisições com `status="200"` para medir a performance do caminho feliz.

### Max Request Duration
- Utilização da métrica `http_server_requests_seconds_max` para identificar picos de lentidão em requisições individuais.
- Configuração de legenda dinâmica usando `{{uri}} {{method}} {{status}}`.

## Monitoramento de Recursos (USE)

### Métricas de Memória (JVM)
- **HEAP USED**: Cálculo do percentual de uso da memória heap comparando `jvm_memory_used_bytes` com `jvm_memory_max_bytes`.
- **NON-HEAP USED**: Monitoramento da área de memória non-heap (metaspace, etc).
- Visualização em **Gauge** com thresholds: 80% (Alerta/Vermelho) e 100% (Crítico/Roxo).



### Utilização e Carga de CPU
- **CPU UTILIZATION**: Comparação entre o uso de CPU do sistema (`system_cpu_usage`) e do processo da aplicação (`process_cpu_usage`).
- **LOAD AVERAGE**: Média de carga do sistema no último minuto (`system_load_average_1m`) comparada ao número total de núcleos disponíveis (`system_cpu_count`).

## Estrutura do Dashboard
- Organização do projeto em **Rows** (Linhas) para facilitar a leitura:
  - **API BASIC**: Métricas fundamentais.
  - **API USE**: Saturação e utilização de recursos de infraestrutura.
  - **API RED**: Experiência do usuário (Taxa, Erros e Duração).
