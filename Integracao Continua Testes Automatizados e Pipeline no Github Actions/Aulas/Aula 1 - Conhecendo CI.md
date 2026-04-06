# Integração Contínua com GitHub Actions

## Introdução à Integração Contínua
A Integração Contínua (CI) permite automatizar a execução de testes e criar rotinas para facilitar o desenvolvimento de software. O objetivo é automatizar tarefas repetitivas, como a execução de testes, para tornar o processo de desenvolvimento mais eficiente.

Neste curso, aprenderemos a configurar uma rotina de CI utilizando **GitHub Actions**, executar testes automatizados e rodar testes em diferentes ambientes.

## Automação de Testes com Docker
A automação de testes em uma aplicação escrita em **Go** pode ser feita utilizando **Docker**. A aplicação pode usar **Postgres** como banco de dados e **pgAdmin** para visualização. O arquivo **docker-compose.yml** define esses serviços.

## Pré-requisitos
- **Go**
- **Docker**
- **Docker Compose**

Esses pré-requisitos são necessários para rodar os testes e a aplicação no ambiente local.

## Configuração do Ambiente
Para configurar o ambiente de testes da aplicação, seguimos os seguintes passos:

1. **Clonamos o projeto do GitHub**:

2. **Corrigimos um erro no arquivo `docker-compose.yml`**, removendo um ponto extra na configuração de volumes.

3. **Subimos o banco de dados** com o comando:
    ```bash
    docker-compose up -d
    ```

4. **Executamos a aplicação** com o comando:
    ```bash
    go run main.go
    ```

6. **Verificamos se a aplicação estava funcionando** acessando o localhost na porta 8080 e adicionando `/leo` ao final da URL:
    ```
    http://localhost:8080/leo
    ```