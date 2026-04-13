# Metricas com Prometheus

## Anatomia de uma Métrica

- **Metric Name (Nome)**: Identifica o aspecto medido (ex: `logback_events_total`).
- **Labels (Rótulos)**: Chave-valor para identificar dimensões da série temporal (ex: `level="info"`, `uri="/topicos"`).
- **Sample (Amostra)**: O valor numérico resultante da consulta em um ponto específico do tempo.
- A combinação do nome com labels específicos define uma série temporal única no banco de dados do Prometheus.

## Tipos de Dados e Estrutura

- **Instant Vector**: Conjunto de séries temporais com uma única amostra para o timestamp atual.
- **Range Vector**: Conjunto de séries temporais com uma lista de amostras retroativas em um intervalo de tempo (ex: `[1m]`).
- **Scalar (Float)**: Valor numérico de ponto flutuante, utilizado para cálculos e geração de gráficos.
- **Scrape Interval**: Frequência com que o Prometheus coleta métricas (ex: 5 segundos). O Unix Timestamp identifica o momento exato de cada coleta.

## Categorias de Métricas (Metric Types)

- **Counter**: Valor cumulativo que apenas aumenta (reseta se a aplicação reiniciar). Útil para histórico de logins e contagem de requisições.
- **Gauge**: Valor que pode subir ou descer arbitrariamente. Ideal para medir uso de CPU, memória e conexões ativas.
- **Histogram**: Agrupa observações (como duração de resposta) em buckets configuráveis (`le` - Less or Equal). Auxilia na medição de SLAs e SLOs.
- **Summary**: Similar ao histograma, mas calcula quantis de forma personalizada no lado da aplicação.

## Seletores e Filtragem em PromQL

- **Operadores de Comparação**:
  - `=`: Igualdade exata.
  - `!=`: Negação (diferente de).
  - `=~`: Correspondência via Regex (ex: `status=~"2..|3.."`).
  - `!~`: Negação via Regex.
- **Filtros de URI**: Uso de `uri!="/actuator/prometheus"` para remover endpoints internos que poluem a visualização de negócio.

## Funções e Agregadores

### Manipulação de Séries
- **increase()**: Calcula a taxa de crescimento de um *Counter* dentro de um *Range Vector* (ex: `increase(...[1m])`).
- **irate()**: Calcula a taxa instantânea de crescimento por segundo olhando os dois últimos pontos de coleta.
- **offset**: Modificador para deslocar a consulta no tempo (ex: `offset 1m` para ver dados de um minuto atrás).

### Agregação de Dados
- **sum()**: Agregador utilizado para somar os resultados de múltiplas séries temporais e obter um valor total único.
- **Exemplo de Cálculo de Requisições/Seg**: `sum(irate(http_server_requests_seconds_count[5m]))`.

## Visualização de Dados

- **Table**: Visualização tabular para conferir valores exatos e labels de cada série.
- **Graph**: Plotagem gráfica das métricas ao longo do tempo. 
- Nota: Expressões do tipo *Range Vector* não podem ser plotadas diretamente; precisam ser convertidas em *Instant Vector* ou *Scalar* via funções.

## Link Documentação Prometheus:
- [https://prometheus.io/docs/prometheus/latest/querying/basics/](https://prometheus.io/docs/prometheus/latest/querying/basics/)
