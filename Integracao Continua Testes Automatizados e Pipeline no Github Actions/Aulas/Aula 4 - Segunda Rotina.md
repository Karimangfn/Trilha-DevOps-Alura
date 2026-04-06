# Organização e Monitoramento de Rotinas de Integração Contínua no GitHub Actions

## Alteração da Ordem das Etapas na CI
A ordem das etapas em uma rotina de integração contínua pode impactar a eficiência do processo. Para evitar desperdício de tempo caso haja erros de lógica no código, os testes devem ser realizados antes da compilação da aplicação.

A rotina é ajustada da seguinte forma:
1. **Configuração do ambiente Go**
2. **Subida do banco de dados**
3. **Execução dos testes**:
   ```bash
   go test -v main_test.go
   ```
4. **Build da aplicação**:
   ```bash
   go build -v main.go
   ```

Manter a indentação correta é essencial para evitar falhas na execução. Além disso, otimizar o tempo de execução das rotinas é uma boa prática, considerando o limite mensal de minutos disponíveis no GitHub Actions.

## Separação das Tarefas de CI
É importante dividir as rotinas de CI em tarefas distintas para melhorar a organização e a legibilidade do código. Em vez de ter todos os comandos em uma única tarefa chamada `build`, a rotina é dividida em duas tarefas: `test` e `build`.

- A tarefa **test** é responsável por preparar o ambiente, subir o banco de dados e rodar os testes automatizados:
  ```bash
  go test -v main_test.go
  ```
- A tarefa **build** se concentra em compilar a aplicação:
  ```bash
  go build -v main.go
  ```

A tag `needs` é usada para garantir que a tarefa `build` só seja executada após a conclusão bem-sucedida da tarefa `test`.

## Monitoramento das Rotinas no GitHub Actions
Através da aba **Actions** do repositório no GitHub, podemos visualizar as execuções das rotinas. Após dividir a rotina em dois jobs (`test` e `build`), focamos na execução mais recente, que foi bem-sucedida (indicada pelo check verde ✅).

## Introduzindo Falhas Intencionais para Teste
Para entender como a rotina de CI lida com falhas, provocamos uma falha intencionalmente em um arquivo de testes automatizados. O processo foi o seguinte:

1. Clonamos o repositório no VS Code.
2. Alteramos uma linha no arquivo `main_test.go`, fazendo com que a resposta esperada não correspondesse à resposta real.

No GitHub, acessamos a aba **Actions** e verificamos a execução:
- Inicialmente, um **círculo amarelo** indica que a rotina está em execução.
- Após a execução, um **X vermelho** aparece, sinalizando um erro.
- Ao clicar no log do job `test`, podemos visualizar detalhes sobre a falha, como a diferença entre o resultado esperado e o obtido.