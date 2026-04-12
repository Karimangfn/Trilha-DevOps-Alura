# Geração de Artefato

## Instalação e Configuração

- **Conceito de Artefato**: É o produto final gerado após a compilação ou processamento do código (ex: um executável, um pacote `.war`, ou uma imagem Docker).
- **Entrega Contínua (CD)**: Processo de automatizar a geração e disponibilização desse artefato após a Integração Contínua (CI) ser bem-sucedida.
- **Action de Upload**: Uso da action oficial `actions/upload-artifact@v4` para armazenar arquivos gerados durante o workflow e permitir seu download posterior.

## Estrutura do Projeto

- **Workflow Atualizado**:
  - Inclusão do passo de `build` para compilar o código-fonte.
  - Inclusão do passo de `upload` para salvar o binário gerado.

## Desenvolvimento da Pipeline

### Compilação do Projeto (Build)

- Em linguagens compiladas (como Go), utiliza-se o comando de compilação diretamente no runner:
  ```yaml
  - name: Build
    run: go build main.go
  ```
- Este comando gera um arquivo binário (neste caso, chamado `main`) dentro do ambiente temporário do GitHub Actions.

### Armazenamento do Artefato

- Para que o arquivo não seja descartado ao fim da execução, utiliza-se a action de upload:
  - `name`: Nome identificador do download no GitHub (ex: `projeto_compilado`).
  - `path`: Caminho do arquivo ou diretório que deve ser salvo (ex: `main`).
  
  ```yaml
  - name: Projeto compilado
    uses: actions/upload-artifact@v4
    with:
      name: projeto_compilado
      path: main
  ```

### Diferenciação por Linguagem

| Tipo de Linguagem | Exemplo | Natureza do Artefato |
| :--- | :--- | :--- |
| **Compilada (Nativa)** | Go, Rust, C++ | Arquivo executável binário. |
| **Compilada (VM)** | Java, C# | Arquivos `.jar`, `.war` ou DLLs. |
| **Interpretada** | Python, PHP, Ruby | O próprio código-fonte ou uma Imagem Docker. |

## Boas Práticas de Entrega Contínua

- **Ambiente de Produção**: O artefato deve ser gerado de acordo com o destino final. Se o destino é Kubernetes, o artefato ideal é uma imagem Docker.
- **Automação de Deploy**: O artefato gerado pode ser enviado automaticamente para servidores via SSH, SCP ou comandos como `kubectl apply`.
- **Verificação de Build**: Realizar o build a cada Pull Request garante que o código não apenas passa nos testes, mas também é capaz de ser compilado sem erros antes de chegar à branch principal.
- **Persistência**: Definir o tempo de retenção dos artefatos no GitHub para economizar espaço de armazenamento, mantendo apenas o necessário para validação.

## Link Documentação:
- [GitHub Actions - Storing workflow data as artifacts](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)
