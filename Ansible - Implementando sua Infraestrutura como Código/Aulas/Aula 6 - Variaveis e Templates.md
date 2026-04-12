# Variáveis, Templates e Roles

## Implementando Variáveis

Para evitar a repetição de informações e facilitar a manutenção do código, o Ansible utiliza variáveis para centralizar dados importantes.

- **group_vars**: Pasta utilizada para organizar arquivos de variáveis por grupos de hosts.
- **all.yml**: Localizado dentro de `group_vars`, armazena variáveis que devem estar disponíveis para todos os grupos de hosts.
- **Sintaxe de Acesso**: O acesso ao valor é feito através de chaves duplas `{{ nome_da_variavel }}`.
- **Aspas**: É obrigatório o uso de aspas quando uma declaração de tarefa começar diretamente com uma variável (ex: `name: '{{ wp_db_name }}'`).

### Delimitação por Escopo
Para aumentar a segurança e evitar conflitos entre rotinas, as variáveis devem ser separadas em arquivos específicos de grupo:
- **wordpress.yml**: Contém variáveis como `db_ip` e `wp_dir`.
- **mysql.yml**: Contém variáveis como `wp_ip` (IP da aplicação com permissão de acesso).
- **Regra de Nomeação**: O nome do arquivo dentro de `group_vars` deve ser exatamente igual ao nome do grupo definido no arquivo de inventário.

---

## Templates com Jinja2

Templates transformam arquivos estáticos em arquivos dinâmicos que podem ser processados pelo Ansible antes de serem enviados ao servidor.

- **Extensão .j2**: Identifica que o arquivo utiliza o motor de template Jinja2.
- **Módulo `ansible.builtin.template`**: Substitui o módulo `copy`. Ele processa as variáveis dentro do arquivo `.j2` e gera o arquivo final no destino.
- **Pasta templates**: Local recomendado para organizar os arquivos de template da infraestrutura.



---

## Modularização com Roles

As **Roles** (Cargos) permitem organizar o código em estruturas independentes, facilitando o reuso e reduzindo o acoplamento do `provisioning.yml`.

### Estrutura de Diretórios
Cada Role possui uma estrutura padronizada onde o Ansible busca automaticamente os arquivos `main.yml`:
- `tasks/`: Contém as tarefas principais da Role.
- `handlers/`: Armazena os manipuladores de eventos (ex: reiniciar serviços).
- `templates/`: Guarda os arquivos `.j2` específicos da Role.
- `meta/`: Define metadados e dependências da Role.

### Execução no Playbook
O arquivo de provisioning torna-se uma chamada simplificada das Roles:

```yaml
- hosts: wordpress
  roles:
    - apache
    - wordpress

- hosts: mysql
  roles:
    - mysql
```

---

## Gerenciamento de Dependências

As dependências garantem que a ordem de execução seja respeitada, impedindo que uma aplicação seja configurada antes de seus requisitos básicos estarem instalados.

- **Configuração**: Definida no arquivo `meta/main.yml` de uma Role.
- **Funcionamento**: Ao chamar a Role `wordpress`, o Ansible verifica a lista de dependências e executa a Role `apache` automaticamente antes de prosseguir.
- **Restrição de Rede**: Não se deve colocar o banco de dados como dependência da aplicação no arquivo `meta` caso eles rodem em **hosts diferentes**, pois o Ansible tentaria instalar o banco na mesma máquina da aplicação.

---

## Links Úteis
- [Documentação oficial do Ansible](https://docs.ansible.com/)
