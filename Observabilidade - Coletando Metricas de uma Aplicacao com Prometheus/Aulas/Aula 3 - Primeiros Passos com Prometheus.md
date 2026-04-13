# Primeiros Passos com Prometheus

## Estrutura da Stack e Redes Docker

- A aplicação foi migrada para um ambiente totalmente conteinerizado para garantir isolamento e segurança entre os serviços.
- **Divisão de Redes**:
  - `database`: Comunicação interna entre API e MySQL.
  - `cache`: Comunicação interna entre API e Redis.
  - `api`: Interliga a API, o Proxy Nginx e o Prometheus.
  - `monit`: Exposição da interface web do Prometheus (porta 9090).
  - `proxy`: Porta de entrada (80) para tráfego externo.
- **Isolamento de Portas**: O uso de `expose` em vez de `ports` no Docker Compose garante que MySQL e Redis sejam acessíveis apenas por outros containers das mesmas redes.

## Configuração do Prometheus (`prometheus.yml`)

- O arquivo define a estratégia de coleta de dados (*scraping*).
- **Configuração Global**:
  - `scrape_interval`: Tempo padrão entre as coletas (definido em 5s).
- **Configuração de Jobs**:
  - `prometheus-forum-api`: Monitoramento do próprio Prometheus no path `/metrics`.
  - `app-forum-api`: Monitoramento da aplicação no path `/actuator/prometheus`.
  - Configurações locais de um *job* sobrescrevem as globais (ex: aumentar o intervalo para 15s em serviços específicos).

## Interface Web do Prometheus

- Acessível via `http://localhost:9090`.
- **Principais Abas**:
  - **Graph**: Espaço para consultas **PromQL** e visualização de gráficos temporais.
  - **Status > Targets**: Validação da saúde dos alvos (se estão *Up* ou *Down*).
  - **Status > Configuration**: Verificação se as alterações no arquivo `.yml` foram aplicadas corretamente.

## Simulação de Tráfego com Client Sintético

- Devido à falta de usuários reais, foi criado o container `client-forum-api` para alimentar as métricas.
- **Estrutura do Dockerfile (Client)**:
  - Baseado em **Debian**.
  - Instalação do **curl** via `apt-get` para realizar as requisições.
  - Configuração de permissões com `chmod +x` no script de automação.
- **Lógica do Script `client.sh`**:
  - Utiliza um laço `while true` (infinito) e variáveis aleatórias (`$RANDOM`) para diversificar o tráfego.
  - **Distribuição de Requisições**:
    - **50%**: Listagem de tópicos (`/topicos`).
    - **30%**: Consulta de tópicos específicos (IDs 1 a 3).
    - **10%**: Autenticação com sucesso.
    - **5%**: Falha de login (senha incorreta).
    - **5%**: Acesso a recursos inexistentes (erro 404).

## Gerenciamento da Stack via Docker Compose

- **Cadeia de Dependências (`depends_on`)**: Garante que os serviços subam na ordem correta: Redis -> MySQL -> App -> Proxy -> Prometheus -> Client.
- **Comandos de Execução**:
  - `docker-compose up -d`: Sobe toda a infraestrutura em segundo plano.
  - `docker-compose build`: Necessário para reconstruir a imagem do Client após descomentar o bloco no arquivo YAML ou alterar o script.
- **Permissões**: O diretório `prometheus_data` exige permissão `777` no Linux para que o container do Prometheus (que não roda como root) consiga gravar os dados no banco TSDB.
