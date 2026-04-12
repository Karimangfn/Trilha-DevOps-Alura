# Container e CI

## Modularização de Rotinas
Para manter o projeto organizado e reutilizável, é uma boa prática separar as responsabilidades em diferentes arquivos de workflow. Em vez de um único arquivo gigante, dividimos a lógica:
* **`go.yml`**: Responsável pelos testes e build da aplicação.
* **`Docker.yml`**: Responsável pela criação e gerenciamento da imagem Docker.

### Chamada de Workflows (Sub-rotinas)
Para conectar os arquivos, utilizamos o gatilho `workflow_call` no arquivo de destino e a instrução `uses` no arquivo principal. Isso permite que um workflow "chame" o outro como se fosse uma sub-rotina.

**No arquivo principal (`go.yml`):**
```yaml
docker:
  needs: build
  uses: ./.github/workflows/Docker.yml
```

---

## O Marketplace do GitHub Actions
O **Marketplace** funciona como uma biblioteca de funções prontas. Em vez de escrever todo o código para configurar o Docker ou fazer login em um serviço, usamos ações (actions) criadas pela comunidade ou por empresas oficiais (como Docker e Amazon).

### Critérios de Escolha:
* **Verificação**: Dê preferência a criadores com o selo azul de verificado (`✅`).
* **Popularidade**: Projetos com mais estrelas/recomendações tendem a ser mais estáveis.
* **Oficiais**: Use ações desenvolvidas pelas próprias empresas das ferramentas (ex: `docker/setup-buildx-action`).



---

## Configuração do Workflow Docker
Ao criar o arquivo `Docker.yml`, alteramos o gatilho de disparo para que ele não rode sozinho em cada *push*, mas sim quando for solicitado pelo workflow principal.

### Estrutura Inicial do `Docker.yml`:
```yaml
name: Docker

on:
  workflow_call: # Permite ser chamado por outro workflow

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3 # Baixa o código do repositório

      - name: setup Docker Build
        uses: docker/setup-buildx-action@v2.0.0 # Prepara o ambiente Buildx
```

### Principais Ações Identificadas:
1. **`actions/checkout`**: Essencial para que o workflow tenha acesso aos arquivos (como o Dockerfile) do repositório.
2. **`docker/setup-buildx-action`**: Configura o Buildx, uma ferramenta avançada do Docker que permite builds mais rápidos e suporte a múltiplas arquiteturas.
3. **`docker/login-action`**: (Próximo passo) Utilizada para autenticar no Docker Hub ou outros registros.

---

## Resumo de Comandos e Gatilhos:
| Termo | Função |
| :--- | :--- |
| **`workflow_call`** | Define que o arquivo é um workflow reutilizável. |
| **`uses` (dentro de steps)** | Chama uma ação do Marketplace ou de um repositório. |
| **`uses` (dentro de jobs)** | Chama um arquivo de workflow local ou remoto. |
| **`needs`** | Garante a ordem de execução (ex: só faz o Docker se o build passar). |

---

**Dica de Organização:** Ao usar `workflow_call`, você pode centralizar segredos e configurações complexas em um único lugar, facilitando a manutenção caso precise alterar a versão do Docker ou o registro de imagens no futuro.
