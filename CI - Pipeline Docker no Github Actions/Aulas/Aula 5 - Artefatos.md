# Artefatos

## Build e Push Automatizado
Com a autenticação resolvida, utilizamos a action `docker/build-push-action` para automatizar o processo de empacotamento e envio da imagem para o Docker Hub.

### Configuração do Workflow:
Para o processo funcionar, definimos o contexto local, o caminho do Dockerfile e ativamos o gatilho de `push`. As tags são geradas dinamicamente usando o nome da branch para manter o versionamento organizado.

```yaml
- name: Build and push Docker images
  uses: docker/build-push-action@v3.1.1
  with:
    context: .
    file: ./Dockerfile
    push: true
    tags: seu_usuario/go_ci:${{ github.ref_name }}
```

---

## O Desafio dos Ambientes Isolados
Cada job no GitHub Actions é executado em um runner (máquina virtual) independente. 
* **O Problema**: O arquivo binário `main` gerado no job de compilação não existe nativamente na máquina que executa o job do Docker.
* **A Falha**: O build do Docker falha ao tentar executar o comando `COPY ./main main` porque o arquivo não foi transportado entre as máquinas.

---

## Uso de Artefatos para Persistência
Artefatos permitem salvar arquivos de um job para que sejam recuperados por outro, servindo como uma "ponte" entre ambientes isolados.

### 1. Upload do Binário (`go.yml`)
No job de compilação, salvamos o resultado para uso futuro:
```yaml
- name: Upload a Build Artifact
  uses: actions/upload-artifact@v3.1.0
  with:
    name: programa
    path: main
```
> **Nota**: Ao gerar artefatos, deve-se remover a estratégia de matriz (`matrix`) do job de build para evitar conflitos de múltiplos arquivos com o mesmo nome tentando ser salvos simultaneamente.

### 2. Download no Job do Docker (`Docker.yml`)
Antes de iniciar o build da imagem, resgatamos o arquivo salvo:
```yaml
- name: Download a Build Artifact
  uses: actions/download-artifact@v3.0.0
  with:
    name: programa
```

---

## Fluxo de Execução no Docker Hub
Após a configuração dos artefatos, o fluxo completo no `Docker.yml` garante que:
1. O ambiente de build do Docker seja preparado (`setup-buildx`).
2. O binário compilado seja baixado para a pasta de trabalho.
3. O login no Docker Hub seja realizado.
4. A imagem seja construída (incluindo o binário recuperado) e enviada ao repositório.

---

## Variáveis Globais Utilizadas
| Variável | Função |
| :--- | :--- |
| `${{ github.ref_name }}` | Captura o nome da branch (ex: `Aula_5`), permitindo que a imagem no Docker Hub tenha a mesma versão da branch. |
| `${{ github.token }}` | Token padrão de autenticação para operações dentro do ecossistema do GitHub. |
