# Protegendo os Segredos

## Instalação e Configuração

- **Exposição de Portas no Docker**: Para que o runner do GitHub Actions acesse o banco de dados rodando em container, é necessário mapear as portas no `docker-compose.yml` (ex: `5432:5432`).
- **GitHub Secrets**: Recurso para armazenar informações sensíveis (senhas, chaves de API, credenciais) de forma encriptada.
  - Localizado em: *Settings > Secrets and Variables > Actions*.
  - Os valores salvos não são visíveis após a criação e são omitidos (mascarados como `***`) nos logs de execução das Actions.

## Estrutura do Projeto

- **Arquivos Alterados**:
  - `docker-compose.yml`: Atualizado para incluir o mapeamento de `ports`.
  - `.github/workflows/go.yml`: Atualizado para consumir as variáveis de ambiente.

## Desenvolvimento da Pipeline

### Configuração de Segredos (Secrets)

- Criação de segredos de repositório para conexão com o banco de dados:
  - `DB_HOST`: Definido como `localhost` (para acesso ao container via host do runner).
  - `DB_PASSWORD`, `DB_USER`, `DB_NAME`, `DB_PORT`.

### Implementação das Variáveis no Workflow

- Uso da sintaxe `${{ secrets.NOME_DO_SEGREDO }}` para injetar os valores no fluxo de trabalho.
- **Passagem de Variáveis via Comando**: Inserção das variáveis diretamente antes do comando de execução no `run`:
  ```yaml
  run: DB_HOST=${{ secrets.DB_HOST }} DB_USER=${{ secrets.DB_USER }} go test -v main_test.go
  ```

### Execução e Validação

- O comando `go test` passa a reconhecer as variáveis de ambiente injetadas, permitindo a conexão com o banco de dados Postgres subido via Docker.
- O GitHub Actions garante que, mesmo que o comando falhe, as senhas injetadas via Secrets nunca apareçam de forma clara nos logs.

## Boas Práticas de Codificação

- **Não usar Hard-coded**: Nunca inserir senhas, usuários ou hosts diretamente no código-fonte ou no arquivo `.yml`.
- **Diferenciação de Ambientes**: Utilizar segredos permite que o mesmo código de teste rode em diferentes infraestruturas apenas alterando os valores no painel do GitHub.
- **Segurança de Logs**: O uso de GitHub Secrets é a maneira profissional de lidar com credenciais, garantindo que informações sensíveis não sejam expostas em ferramentas de integração contínua.
- **Merge Seguro**: Realizar o *Squash and merge* apenas após todos os checks (Lint e Testes) apresentarem status positivo (verde).

## Link Documentação: 
- [https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
