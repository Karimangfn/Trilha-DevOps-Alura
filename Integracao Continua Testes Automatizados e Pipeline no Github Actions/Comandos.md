# Comandos Importantes do GitHub Actions

## Estratégia de Matrizes
- **matrix** – Permite rodar testes em diferentes versões ou sistemas operacionais em um único job, sem criar rotinas separadas.
  - Exemplo:
    ```yaml
    strategy:
      matrix:
        version: [1.0, 2.0, 3.0]
    ```

- **${{ matrix.<variável> }}** – Referência a valores definidos na matriz para configurar os jobs.
  - Exemplo:
    ```yaml
    - name: Set up environment
      uses: actions/setup-env@v2
      with:
        version: ${{ matrix.version }}
    ```

## Segredos no GitHub Actions
- **${{ secrets.<NOME_DO_SEGREDO> }}** – Referência a segredos armazenados no repositório (como senhas ou chaves) para não expô-los diretamente no código.
  - Exemplo:
    ```yaml
    - name: Set up credentials
      run: echo "${{ secrets.MY_SECRET }}" > ~/.secret_key
    ```

## Jobs e Tarefas
- **jobs** – Define os trabalhos dentro de uma rotina do GitHub Actions.
  - Exemplo:
    ```yaml
    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Run tests
            run: ./run_tests.sh
    ```

- **needs** – Especifica a dependência de um job anterior para a execução de um novo job.
  - Exemplo:
    ```yaml
    jobs:
      build:
        needs: test
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Build the application
            run: ./build.sh
    ```

## Condições de Execução
- **if** – Define condições para a execução de etapas ou jobs.
  - Exemplo:
    ```yaml
    - name: Run tests
      if: success()
      run: ./run_tests.sh
    ```

## Ações Comuns
- **actions/checkout** – Faz o checkout do código do repositório no pipeline.
  - Exemplo:
    ```yaml
    - uses: actions/checkout@v2
    ```

- **actions/setup-env** – Configura o ambiente necessário para execução (por exemplo, para Python, Node.js, etc.).
  - Exemplo:
    ```yaml
    - uses: actions/setup-node@v2
      with:
        node-version: '14'
    ```

## Fases de Execução
- **runs-on** – Define o sistema operacional onde o job será executado (ex.: `ubuntu-latest`, `windows-latest`, etc.).
  - Exemplo:
    ```yaml
    runs-on: ubuntu-latest
    ```

- **steps** – Define as etapas que serão executadas em um job.
  - Exemplo:
    ```yaml
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: ./install_dependencies.sh
    ```

## Exemplo de Fluxo de Trabalho
Aqui está um exemplo genérico de um fluxo de trabalho no GitHub Actions, utilizando matrizes, segredos e dependências entre jobs:

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        version: [1.0, 2.0, 3.0]
    steps:
      - uses: actions/checkout@v2
      - name: Set up environment
        uses: actions/setup-env@v2
        with:
          version: ${{ matrix.version }}
      - name: Run tests
        run: ./run_tests.sh

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build the application
        run: ./build.sh
