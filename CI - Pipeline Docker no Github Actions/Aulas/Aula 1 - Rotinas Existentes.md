# Rotinas Existentes

## Introdução ao GitHub Actions
O **GitHub Actions** é a ferramenta de CI utilizada para automatizar fluxos de trabalho diretamente no repositório. O objetivo é garantir que processos como testes e compilação (*build*) sejam executados automaticamente em todas as etapas do desenvolvimento.

### Conceitos Fundamentais:
* **Workflows**: Definidos em arquivos `.yml` dentro da pasta `.github/workflows`.
* **Jobs**: Tarefas específicas (ex: testar, buildar) que compõem o workflow.
* **Steps**: Passos individuais dentro de um job (ex: preparar ambiente, rodar comando).

---

## Configuração de Gatilhos (Triggers)
Para garantir que a integração contínua ocorra durante todo o desenvolvimento, é uma boa prática configurar a rotina para ser executada em **qualquer branch**.

### Ajuste de Branches
No arquivo de configuração (ex: `go.yml`), utiliza-se o caractere curinga (`*`) entre aspas simples para representar todas as ramificações:

```yaml
on:
  push:
    branches: [ '*' ]
  pull_request:
    branches: [ '*' ]
```

---

## Estratégias de Matriz (Matrix Strategy)
A estratégia de matriz permite que um mesmo job seja executado em múltiplas combinações de ambientes, como diferentes versões de linguagens ou sistemas operacionais (SO).

### Teste em Múltiplos Sistemas Operacionais
Testar em diferentes versões de SO (como `ubuntu-latest` e `ubuntu-18.04`) previne falhas de compatibilidade e vulnerabilidades de sistemas desatualizados.



#### Exemplo de implementação:
```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        go_version: ['1.18', '1.17']
        os: ['ubuntu-latest', 'ubuntu-18.04']
```

---

## Limitações e Escopo da Matriz
Um ponto crítico no GitHub Actions é que a declaração de uma `strategy` tem **escopo local**, ou seja, ela pertence apenas ao job onde foi definida.

* **O Problema**: Se o job `build` depende do job `test` (`needs: test`) e tenta acessar `${{ matrix.os }}`, ele retornará um valor vazio caso a matriz não seja declarada novamente dentro do bloco `build`.
* **A Solução**: Deve-se replicar a estratégia de matriz em cada job que necessite rodar em múltiplos ambientes.

```yaml
build:
  needs: test
  runs-on: ${{ matrix.os }}
  strategy:
    matrix:
      os: ['ubuntu-latest', 'ubuntu-18.04']
  steps:
    - uses: actions/checkout@v3
    - name: Build
      run: go build -v main.go
```

---

## Preparação do Ambiente
Para o desenvolvimento deste curso, é necessária a instalação das seguintes ferramentas:

| Ferramenta | Descrição |
| :--- | :--- |
| **Docker** | Plataforma de containers para isolar a aplicação. |
| **WSL 2** | Necessário para usuários Windows rodarem o Docker com performance. |
| **Go (Golang)** | Linguagem de programação utilizada no projeto base. |

### Dica de Manutenção:
Sistemas operacionais, especialmente versões **LTS (Long Term Support)** do Ubuntu, possuem suporte limitado (geralmente 5 anos). Testar antecipadamente em novas versões evita que a aplicação fique vulnerável após o fim do suporte do SO original.
