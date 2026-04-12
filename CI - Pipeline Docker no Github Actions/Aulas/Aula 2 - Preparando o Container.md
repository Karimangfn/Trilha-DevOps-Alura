# Preparando o Container

## Criação Manual do Dockerfile
Para automatizar o processo de criação de containers, primeiro é necessário entender a construção manual através do **Dockerfile**. Este arquivo contém as instruções para montar a imagem da aplicação.

### Estrutura do Dockerfile:
* **FROM**: Define a imagem base (ex: `ubuntu:latest`).
* **EXPOSE**: Indica a porta em que a aplicação se comunica (ex: `8000`).
* **WORKDIR**: Define o diretório de trabalho dentro do container (ex: `/app`).
* **COPY**: Transfere arquivos da máquina local para o container. Recomenda-se copiar a versão já compilada do programa para otimizar o tamanho da imagem.
* **CMD**: Define o comando principal que será executado ao iniciar o container.

```dockerfile
FROM ubuntu:latest
EXPOSE 8000
WORKDIR /app
# Variáveis de ambiente padrão
ENV HOST=localhost PORT=5432
ENV USER=root PASSWORD=root DBNAME=root
COPY ./main main
CMD [ "./main" ]
```

---

## Variáveis de Ambiente e Conexão com Banco
Em ambientes de produção ou nuvem, o banco de dados raramente está na mesma máquina que a aplicação. Por isso, é fundamental substituir configurações fixas (*hardcoded*) por **variáveis de ambiente**.

### Implementação em Go:
Utiliza-se a biblioteca `os` e a função `os.Getenv()` para capturar os valores do sistema operacional e concatená-los na string de conexão.



---

## Otimização com Camadas (Layers) e Cache
O Docker constrói imagens em camadas. Se uma linha do Dockerfile não for alterada, o Docker utiliza o **cache**, acelerando o processo de *build*.

* **Dica de Performance**: Coloque instruções que mudam pouco (como `ENV` e `WORKDIR`) antes de instruções que mudam frequentemente (como `COPY` do código-fonte). Assim, apenas as camadas finais serão recriadas a cada atualização.

---

## Ajuste na Rotina de CI (GitHub Actions)
Ao alterar a aplicação para exigir variáveis de ambiente, os testes automatizados no GitHub Actions podem falhar por não encontrarem essas informações. É necessário replicar as configurações no arquivo `.yml` do workflow.

### Configuração de `env` no GitHub Actions:
Diferente do Dockerfile, no GitHub Actions utiliza-se dois pontos (`:`) para atribuir valores:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    env:
      HOST: localhost
      PORT: 5432
      USER: root
      PASSWORD: root
      DBNAME: root
```

---

## Segurança: Desenvolvimento vs. Produção
Embora o Dockerfile contenha valores padrão como `root` para facilitar o desenvolvimento, esses dados **devem ser alterados** em ambiente de produção através da sobreposição das variáveis de ambiente na execução do container.

### Comparativo de Vantagens:
| Característica | Máquina Virtual | Container Docker |
| :--- | :--- | :--- |
| **Velocidade** | Minutos para iniciar | Segundos para iniciar |
| **Complexidade** | SO Completo | Apenas o necessário |
| **Segurança** | Atualização manual por VM | Atualização via Imagem Base |
