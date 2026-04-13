# Preparando o Terreno

Nesta etapa, focamos na construção de uma **esteira de processos (CI Pipeline)** para garantir que o código seja integrado com segurança. Uma esteira típica de Integração Contínua segue uma ordem lógica de validação.

---

## 1. Ferramenta de Qualidade (Lint)
O primeiro passo é garantir que o código segue as convenções e não possui erros estruturais óbvios. Para o Go, utilizamos o `golangci-lint`.

### Execução via Docker
Para evitar instalações locais complexas, rodamos a ferramenta através de um container:
```bash
docker run --rm -itv $(pwd):/app -w /app golangci/golangci-lint golangci-lint run controllers/ database/ models/ routes/
```
* **`--rm`**: Remove o container após o uso.
* **`-v $(pwd):/app`**: Mapeia a pasta atual para dentro do container.
* **`-w /app`**: Define o diretório de trabalho.

> **Importante:** Em automações, o sucesso de um processo é indicado pelo **Exit Status 0**. Se o Lint encontrar erros, ele retornará um valor diferente de 0 (ex: 1), o que sinaliza à esteira que a integração deve ser interrompida. Você pode verificar o status no Linux/Mac com o comando `echo $?`.

---

## 2. Testes Automatizados
Após validar a qualidade, executamos os testes funcionais para garantir que as rotas da API (como a de listagem de alunos e saudação) estão operando corretamente.

### Comandos de Teste:
```bash
docker compose up -d                        # Garante que a aplicação está rodando
docker compose exec app go test main_test.go # Executa os testes dentro do container
```

---

## 3. Automatização Local com Makefile
Para evitar digitar comandos longos repetidamente, utilizamos o **Makefile**. Ele permite agrupar tarefas e executá-las com nomes simples.

### Estrutura do `Makefile`:
No Makefile, usamos a variável `$(CURDIR)` para identificar o diretório atual de forma compatível com a ferramenta.

```makefile
lint:
	docker run --rm -v $(CURDIR):/app -w /app golangci/golangci-lint golangci-lint run controllers/ database/ models/ routes/

test:
	docker compose exec app go test main_test.go

start:
	docker compose up -d

ci: start lint test
```

### Como utilizar:
* **`make lint`**: Executa apenas a verificação de qualidade.
* **`make test`**: Executa apenas os testes.
* **`make ci`**: Executa a esteira completa (**start -> lint -> test**). Se uma etapa falhar, as seguintes não são executadas.

---

## Por que usar um Servidor de Integração?
Embora a automação local via Makefile seja útil, ela depende da máquina da pessoa desenvolvedora. Em projetos reais, as suítes de testes podem ser pesadas e demoradas. Para resolver isso, utilizamos um **Servidor de CI** (como o GitHub Actions) para processar essas tarefas de forma independente e centralizada.
