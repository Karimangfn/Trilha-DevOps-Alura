# Estratégias Avançadas no GitHub Actions

## Estratégia de Matrizes no GitHub Actions
A estratégia de matrizes no GitHub Actions permite testar uma aplicação em diferentes ambientes sem a necessidade de criar rotinas separadas. Isso é útil para rodar jobs em múltiplos sistemas operacionais e versões de linguagens.

### Implementação de Matrizes para Versões do Go
Vimos como utilizar a estratégia de matrizes para testar uma aplicação em diferentes versões do Go. Definimos uma matriz com as versões `1.18`, `1.17`, e versões maiores ou iguais a `1.18`. A estrutura de referência da matriz foi configurada utilizando a sintaxe `${{ matrix.go_version }}` para configurar o ambiente do Go.

A matriz permite configurar um ambiente específico para cada versão do Go, sem a necessidade de escrever jobs separados para cada um. Aqui está um exemplo de como a configuração pode ser feita:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        go_version: [1.18, 1.17, '>=1.18']
    steps:
      - uses: actions/checkout@v2
      - name: Set up Go
        uses: actions/setup-go@v2
        with:
          go-version: ${{ matrix.go_version }}
      - name: Run tests
        run: go test -v main_test.go
```

## Utilização de Segredos no GitHub Actions
No GitHub Actions, é possível utilizar segredos, que são variáveis de ambiente criptografadas. Esses segredos são essenciais para armazenar informações sensíveis, como senhas e chaves, sem expô-las diretamente no código-fonte, o que é uma prática de segurança importante.

### Criação de Segredos
Para criar um segredo, basta acessar as configurações do repositório, adicionar um ambiente e, em seguida, criar o segredo desejado. Uma vez criado, o segredo pode ser referenciado no código com a sintaxe `${{ secrets.NOME_DO_SEGREDO }}`.

### Segurança e Boas Práticas
Os segredos não são clonados junto com o repositório, garantindo a segurança das informações. É fundamental evitar a exposição de segredos em logs durante a execução das rotinas de CI. Isso ajuda a proteger dados sensíveis de acessos não autorizados.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up SSH key
        run: echo "${{ secrets.SSH_KEY }}" > ~/.ssh/id_rsa
```