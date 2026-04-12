# GitHub Actions

## Instalação e Configuração

- O **GitHub Actions** permite executar comandos e automações diretamente nos servidores do GitHub.
- Para configurar um fluxo, deve-se criar um arquivo `.yml` dentro do diretório `.github/workflows/`.
- É possível criar workflows manualmente ou utilizar templates prontos (ex: template de **Go** fornecido pelo GitHub).
- Servidores do GitHub Actions (runners) já possuem ferramentas como **Docker**, **Git** e **Python** pré-instalados por padrão.

## Estrutura do Projeto

- **Diretórios de Automação**:
  - `.github/workflows/go.yml`: Arquivo de configuração da pipeline de Integração Contínua (CI).
- **Componentes do Workflow**:
  - `name`: Nome do workflow (ex: "Integração Contínua").
  - `on`: Define os gatilhos de execução (ex: `push` ou `pull_request` na branch `main`).
  - `jobs`: Conjunto de tarefas que podem rodar em paralelo.
  - `steps`: Sequência de etapas dentro de um job.

## Desenvolvimento da Pipeline

### Configuração do Ambiente

- Utilização da action `actions/checkout@v4` para clonar o repositório no servidor.
- Uso de `actions/setup-go@v4` para instalar uma versão específica da linguagem (ex: Go 1.22).

### Implementação dos Jobs

- **Inicialização do Banco de Dados**:
  - Execução de comandos Docker diretamente no runner: `docker compose up -d postgres`.
- **Análise Estática (Lint)**:
  - Uso de actions externas como `golangci/golangci-lint-action@v6`.
  - Passagem de argumentos via `with: args:` para filtrar pastas específicas (ex: `controllers/`, `database/`).
- **Execução de Testes**:
  - Comando `go test -v main_test.go` para validar as funcionalidades.

### Variáveis de Ambiente e Segredos

- Necessidade de configurar variáveis de ambiente distintas para o ambiente local e para o CI (GitHub Actions) para garantir a conexão com serviços como bancos de dados.

## Boas Práticas de Integração Contínua

- **Proteção de Branch**:
  - Configurar **Rulesets** ou **Branch Protection Rules** em *Settings > Branches*.
  - Bloquear `push` direto na branch `main`.
  - Exigir a criação de um **Pull Request** antes do merge.
  - Exigir que o status da verificação (`status checks`) passe obrigatoriamente (ex: o job `ci` deve retornar sucesso).
- **Uso de Actions Oficiais**:
  - Preferir o uso de `uses` (actions da comunidade/oficiais) em vez de comandos `run` complexos para ferramentas conhecidas como Linters.
- **Workflow de Trabalho**:
  - Desenvolver em branches separadas, abrir Pull Requests e aguardar a validação automática antes da integração final.

## Links Úteis:
- [Documentação oficial do GitHub Actions](https://docs.github.com/en/actions)
- [Repositório da Action GolangCI-Lint](https://github.com/golangci/golangci-lint-action)
