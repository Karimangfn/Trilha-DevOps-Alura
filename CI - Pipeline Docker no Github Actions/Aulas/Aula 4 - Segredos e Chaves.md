# Segredos e Chaves

## Autenticação com Docker Login
Para que o GitHub Actions consiga enviar a imagem da aplicação para um repositório remoto (como o **Docker Hub**), é necessário realizar a autenticação. A ação oficial `docker/login-action` é utilizada para este fim.

### Configuração no Workflow:
Ao utilizar essa ação, evite expor informações sensíveis. Parâmetros como `registry` (se omitido, o padrão é o Docker Hub) e `logout` (que remove as credenciais após o uso) ajudam a manter a segurança.

```yaml
- name: Docker Login
  uses: docker/login-action@v2.0.0
  with:
    username: seu_usuario  # Seu login do Docker Hub
    password: ${{ secrets.PASSWORD_DOCKER_HUB }} # Referência ao segredo
```

---

## GitHub Secrets
Os **Secrets** são variáveis de ambiente criptografadas que permitem armazenar dados sensíveis (senhas, tokens de API, chaves SSH) com segurança.

### Vantagens do uso de Secrets:
* **Invisibilidade**: Uma vez salvo, o valor não pode ser visualizado, apenas atualizado ou removido.
* **Isolamento**: Ao clonar ou baixar um repositório, os segredos **não** são incluídos no download.
* **Mascaramento**: No log de execução do GitHub Actions, o valor do segredo é substituído por asteriscos (`***`).

### Como criar um Secret:
1. Vá em **Settings** > **Security** > **Secrets and variables** > **Actions**.
2. Clique em **New repository secret**.
3. Defina um nome (ex: `PASSWORD_DOCKER_HUB`) e insira o valor.

---

## Herança de Segredos (Secrets Inherit)
Por padrão, quando um workflow chama outro (sub-rotina), os segredos **não são compartilhados** automaticamente por questões de segurança. Para que o arquivo `Docker.yml` consiga acessar a senha salva no repositório, é necessário informar explicitamente que ele deve "herdar" essas credenciais.

**No arquivo principal (`go.yml`):**
```yaml
docker:
  needs: build
  uses: ./.github/workflows/Docker.yml
  secrets: inherit # Permite que o workflow chamado acesse os Secrets
```

---

## Resumo de Segurança e Boas Práticas:
| Ação | Por que fazer? |
| :--- | :--- |
| **Não expor senhas** | Impede que qualquer pessoa com acesso ao código roube suas credenciais. |
| **Usar `logout: true`** | Garante que a sessão seja encerrada no runner do GitHub após o término do job. |
| **Herança explícita** | Mantém o controle de quais fluxos de trabalho têm permissão para acessar dados críticos. |

---

## Próximos Passos
Com o login efetuado com sucesso, o pipeline está pronto para o estágio final: construir a imagem Docker e realizar o **Push** (envio) para o repositório oficial, tornando a aplicação disponível para deploy.
