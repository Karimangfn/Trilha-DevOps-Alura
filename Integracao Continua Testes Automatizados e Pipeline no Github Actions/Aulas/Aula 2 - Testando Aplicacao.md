# Testes Automatizados e Integração Contínua com GitHub Actions

## Testes Automatizados
Os testes automatizados são essenciais para garantir que a lógica da aplicação funcione corretamente e para identificar possíveis bugs. Eles verificam se o comportamento da aplicação está conforme o esperado, sinalizando erros de lógica.

## Execução dos Testes
Executamos os testes utilizando os comandos:

```bash
go test main_test.go
```

E para uma saída mais detalhada:

```bash
go test -v main_test.go
```

## Modificação de Testes
Modificamos um dos testes no arquivo main_test.go e observamos como a falha no teste alertou sobre alterações inesperadas no comportamento da aplicação.

Automatizar os testes é uma prática essencial para garantir um desenvolvimento eficiente e evitar regressões no código.

## GitHub Actions para Integração Contínua
O GitHub Actions é uma ferramenta de Integração Contínua (CI) que permite automatizar tarefas como build, testes e deploy. Ao criar um repositório no GitHub, podemos acessar a aba Actions, onde diversas opções de workflows são sugeridas, incluindo workflows para Go.

Esses workflows oferecem sugestões específicas para diferentes linguagens e frameworks, além de recursos para deployment, segurança e automação.

## Criando a Rotina de CI no GitHub Actions
Para a nossa aplicação, começamos a criar a rotina de Integração Contínua (CI) no GitHub Actions. Em vez de criar uma rotina do zero, utilizamos a rotina sugerida para Go.

## Jobs no GitHub Actions
Os jobs são os passos que serão executados na rotina de CI, e cada job pode realizar tarefas como rodar testes, compilar o código ou fazer deploy.