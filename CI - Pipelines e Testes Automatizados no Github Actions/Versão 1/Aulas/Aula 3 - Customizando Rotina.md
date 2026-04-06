# Configuração de Integração Contínua com GitHub Actions

## Configuração Inicial do Job
O job chamado `build` é definido na seção `jobs` e executado em um ambiente Ubuntu mais recente.  

Os passos do job são listados sob `steps`:
1. **Checkout**: Utiliza a ação `checkout` para configurar a máquina.
2. **Setup Go**: Configura a versão desejada do Go (alterada para 1.18).
3. **Build**: Compila o código.
4. **Test**: Executa os testes automatizados.  

A tag `run` é utilizada para rodar os comandos necessários na máquina.

## Customização da Rotina
Na customização da rotina do GitHub Actions, configuramos os passos para build e execução de testes automatizados.  

- Para fazer o build do projeto:
  ```bash
  go build -v main.go
  ```
- Para rodar os testes:
  ```bash
  go test -v main_test.go
  ```

Os arquivos do repositório são copiados automaticamente para um diretório temporário no GitHub Actions. Caso os arquivos estejam em subpastas, é necessário indicar o caminho relativo, como `routes/main.go`.

## Configuração do Banco de Dados com Docker
Preparamos a rotina para subir um banco de dados de teste utilizando Docker em um contêiner. Os passos configurados incluem:

1. **Build**: Compila o programa.
   ```bash
   go build -v main.go
   ```
2. **Build-DB**: Prepara as imagens necessárias do banco de dados.
   ```bash
   docker-compose build
   ```
3. **Create-DB**: Sobe o banco de dados.
   ```bash
   docker-compose up -d
   ```
4. **Test**: Executa os testes.
   ```bash
   go test -v main_test.go
   ```