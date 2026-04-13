# Aprofundando em Métricas

## Introdução à Camada API RED
Neste capítulo, o foco mudou da infraestrutura básica para a experiência do usuário final, utilizando a metodologia **RED**:
* **R**ate (Taxa): Volume de requisições.
* **E**rrors (Erros): Quantidade e taxa de falhas.
* **D**uration (Duração): Tempo de resposta dos endpoints.

---

## Desenvolvimento de Painéis: API BASIC

### Métricas de Autenticação
* **USERS LOGGED**:
    * **Métrica**: `increase(auth_user_success_total[1m])`
    * **Visualização**: **Stat**. Arredondado para 0 casas decimais. Representa uma aproximação de usuários autenticados no último minuto.
* **AUTH ERRORS**:
    * **Métrica**: `increase(auth_user_error_total[1m])`
    * **Thresholds**: 5 (Laranja - situação incomum), 10 (Vermelho - possível problema de integração/banco).

### Monitoramento de Conexões (JDBC/HikariCP)
* **CONNECTION STATE**:
    * **Métricas**: Agrupamento de `hikaricp_connections_active`, `hikaricp_connections_idle` e `hikaricp_connections_pending`.
    * **Visualização**: **Time Series** com legenda em formato **Table** à direita (Min, Max, Last).
* **DB CONNECTION TIMEOUT**:
    * **Métrica**: `increase(hikaricp_connections_timeout_total[1m])`
    * **Threshold**: Alerta vermelho a partir de 2 timeouts por minuto.

---

## Desenvolvimento de Painéis: API RED

### 1. Total Requests (Rate)
* **Métrica**: `sum(increase(http_server_requests_seconds_count{uri!="/actuator/prometheus"}[1m]))`
* **Configuração**: Visualização **Stat**, ignorando o endpoint de scrape para focar no tráfego real do usuário.

### 2. Response Time (Duration)
* **Métrica**: `rate(http_server_requests_seconds_sum{...}[1m]) / rate(http_server_requests_seconds_count{...}[1m])`
* **Unidade**: `Time > seconds(s)`.
* **Legenda**: `{{uri}} {{method}} {{status}}` em formato de tabela para identificar gargalos em endpoints específicos.

### 3. Error 500 (Errors - Absoluto)
* **Métrica**: `sum(increase(http_server_requests_seconds_count{status="500", uri!="/actuator/prometheus"}[1m]))`
* **Visualização**: **Stat**.
* **Threshold**: Alerta vermelho caso ocorram 3 ou mais erros no último minuto. Agrega diferentes exceções (como `QueryTimeout`) em um único valor.

### 4. Error Rate (Errors - Relativo)
Para entender a proporção de falhas em relação ao tráfego total, utiliza-se a divisão aritmética entre a taxa de erros e a taxa total de requisições.
* **Métrica (Exemplo Erro 500)**:
    `sum(rate(http_server_requests_seconds_count{status="500",...}[5m])) / sum(rate(http_server_requests_seconds_count{...}[5m]))`
* **Configuração**:
    * **Queries adicionais**: Criadas para status `400` e `404`.
    * **Unidade**: `Misc > Percent (0.1-1.0)`.
    * **Cores da Legenda**: Vermelho (500), Laranja (400), Amarelo (404).
---
## Funções PromQL Utilizadas
| Função | Uso Prático |
| :--- | :--- |
| `increase()` | Utilizada para contadores em painéis **Stat**, focando no crescimento absoluto (ex: total de logs). |
| `rate()` | Calcula a taxa de variação por segundo. Essencial para métricas de tempo de resposta e taxas de erro. |
| `sum()` | Agrega múltiplas séries temporais em uma única série, permitindo visões globais da aplicação. |

## Testes e Validação
Durante as aulas, foi realizado o teste de interrupção dos serviços (`docker container stop` para MySQL e Redis).
* **Resultado**: O painel `JDBC Pool` zerou instantaneamente, enquanto `DB Connection Timeout` e `Error 500` refletiram a falha de infraestrutura no minuto seguinte, validando a eficácia do monitoramento.
