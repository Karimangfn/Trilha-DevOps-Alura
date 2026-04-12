# Recapitulando

A integração contínua (CI) é uma cultura de desenvolvimento voltada para projetos em equipe, onde as alterações de código são integradas à branch principal (`main`) de forma frequente — idealmente várias vezes ao dia. O objetivo é detectar conflitos rapidamente e garantir a estabilidade do sistema através de processos automatizados de testes e qualidade.



## Ferramentas e Ambiente
O curso utiliza um conjunto de ferramentas essenciais para a prática de CI:
* **Visual Studio Code (VSC):** Editor de código.
* **Git:** Para controle de versão, incluindo as funções de *fork* (cópia para sua conta) e *clone* (download local).
* **Docker e Docker Compose:** Para criação de ambientes isolados e reprodutíveis.

### Comandos de Verificação:
```bash
git --version      # Verifica a instalação do Git
docker --version   # Verifica a instalação do Docker
```

---

## Execução da Aplicação (Projeto Go)
O projeto consiste em uma API em **Go (Golang)** que gerencia alunos. A execução local é feita via Docker Compose:

| Comando | Função |
| :--- | :--- |
| `docker compose up` | Sobe os containers e exibe logs no terminal. |
| `docker compose up -d` | Sobe os containers em segundo plano. |
| `docker compose down` | Para e remove os containers. |
| `docker compose logs -f app` | Segue os logs em tempo real do serviço da aplicação. |

A aplicação, por padrão, responde no endereço `localhost:8080/alunos`.

---

## Variáveis de Ambiente e Portabilidade
Para que a aplicação funcione em múltiplos ambientes (local, teste, produção) sem alteração no código-fonte, utiliza-se o conceito de variáveis de ambiente.

### Ajuste no Código (`db.go`):
O código foi alterado para utilizar o pacote `os` do Go, capturando os dados de conexão de forma dinâmica:
```go
import "os"
// ...
endereco := os.Getenv("DB_HOST")
usuario  := os.Getenv("DB_USER")
```

### Ajuste no `docker-compose.yml`:
Os valores são injetados através da seção `environment`, permitindo a portabilidade do sistema:
```yaml
app:
  environment:
    - DB_HOST=postgres
    - DB_USER=root
    - DB_PASSWORD=root
    - DB_NAME=root
    - DB_PORT=5432
```
