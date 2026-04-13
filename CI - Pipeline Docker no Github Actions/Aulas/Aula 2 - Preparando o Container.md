# Preparando o Container

## Criação do Dockerfile
Para automatizar o processo, primeiro construímos a imagem da aplicação através do **Dockerfile**. Ele serve como a "receita" para o container.

### Comandos de Preparação:
Antes de buildar a imagem, é necessário clonar o projeto e gerar o executável da aplicação:
```bash
git clone https://github.com/alura-cursos/Curso_CI_2.git
cd Curso_CI_2/
go build main.go  # Gera o binário executável 'main'
```

### Estrutura do Dockerfile:
* **FROM**: Imagem base (ex: `ubuntu:latest`).
* **EXPOSE**: Porta de comunicação (ex: `8000`).
* **WORKDIR**: Diretório interno (`/app`).
* **ENV**: Define variáveis de ambiente.
* **COPY**: Transfere o binário compilado para dentro da imagem.
* **CMD**: Comando executado ao iniciar o container.

```dockerfile
FROM ubuntu:latest
EXPOSE 8000
WORKDIR /app
ENV HOST=localhost PORT=5432
ENV USER=root PASSWORD=root DBNAME=root
COPY ./main main
CMD [ "./main" ]
```

---

## Variáveis de Ambiente e Conexão
Para que a aplicação seja portátil (funcione em Nuvem ou Local), substituímos valores fixos de conexão por variáveis de ambiente usando a biblioteca `os` do Go:

**Exemplo de implementação no código (`db.go`):**
```go
stringDeConexao := "host="+os.Getenv("HOST")+" user="+os.Getenv("USER")+" ..."
```

---

## Otimização de Camadas (Cache)
O Docker constrói imagens em camadas (*layers*). 
* **Boa Prática**: Coloque instruções que mudam pouco (como `WORKDIR` e `ENV`) **antes** do `COPY`. 
* Isso garante que, ao alterar apenas o código-fonte, o Docker use o **cache** para as camadas anteriores, tornando o `docker build .` muito mais rápido.

---

## Ajuste na Rotina de CI (GitHub Actions)
Como a aplicação agora exige variáveis de ambiente para conectar ao banco, os testes no GitHub Actions irão falhar se essas variáveis não estiverem no `go.yml`.

**Configuração no Workflow:**
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

## Comandos Úteis de Execução:
| Ação | Comando |
| :--- | :--- |
| **Buildar Imagem** | `docker build .` |
| **Listar Imagens** | `docker images` |
| **Compilar Go** | `go build main.go` |

---

## Links Úteis
* [Documentação Docker: Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
* [Go os.Getenv Documentation](https://pkg.go.dev/os#Getenv)
