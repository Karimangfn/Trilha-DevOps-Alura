# Instalando o Banco de Dados

## Instalação e Configuração

- **Dependências de Comunicação**: Instalação do pacote `python3-pymysql` via `apt` para permitir que o Ansible se comunique com o MySQL.
- **Módulos de Comunidade**: Uso da coleção `community.mysql` para gerenciar recursos de banco de dados que não estão nos módulos nativos (`builtin`).
- **Autenticação**: Uso de `login_unix_socket: /run/mysqld/mysqld.sock` e `become: yes` para realizar operações administrativas como `root`.

## Estrutura do Projeto

- **Organização de Tarefas**: Inclusão de novos blocos no `provisioning.yml` para a camada de persistência.
- **Segurança de Acesso**: Separação de privilégios, evitando que a aplicação utilize o usuário `root` do banco de dados.

## Implementação da Infraestrutura

### Criação do Banco de Dados

- Utilização do módulo `community.mysql.mysql_db` para garantir a presença da base de dados.
```yaml
- name: Create a new database with name 'wordpress_db'
  community.mysql.mysql_db:
    name: wordpress_db
    state: present
    login_unix_socket: /run/mysqld/mysqld.sock
  become: yes
```

### Gestão de Usuários e Privilégios

- Uso do módulo `community.mysql.mysql_user` para criar o usuário da aplicação com permissões específicas (`SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE`, `DROP`, `ALTER`).
- **Boas Práticas**: Definição de senhas seguras e restrição de acesso apenas às tabelas necessárias (`wordpress_db.*`).

### Configuração do wp-config.php

- **Cópia Remota**: Uso de `ansible.builtin.copy` com `remote_src: yes` para gerar o arquivo de configuração a partir do modelo (`wp-config-sample.php`) dentro do servidor.
- **Automatização de Substituições**: Uso do módulo `ansible.builtin.replace` com o laço `with_items` para configurar as credenciais de banco de dados de forma otimizada.

## Otimização e Segurança

### Laços de Repetição (Loops)

- Uso de `with_items` e a variável `{{ item }}` para evitar a repetição de código em tarefas similares (como substituir múltiplos campos de texto).
```yaml
- name: Configure wp-config with database
  ansible.builtin.replace:
    path: /srv/www/wordpress/wp-config.php
    regexp: '{{ item.regexp }}'
    replace: '{{ item.replace }}'
  with_items:
    - { regexp: 'database_name_here', replace: 'wordpress_db'}
    - { regexp: 'username_here', replace: 'wordpress_user'}
    - { regexp: 'password_here', replace: '12345'}
  become: yes
```

### Vulnerabilidade "Known Secrets"

- **Conceito**: Proteção contra ataques que visam extrair dados criptografados caso o banco seja comprometido.
- **Solução**: Uso de *Salts* (frases únicas) concatenadas às senhas antes da geração do *hash*.
- **Implementação**: Uso do módulo `ansible.builtin.lineinfile` com `search_string` para substituir as chaves de segurança padrão por valores únicos gerados pela API do WordPress.

## Links Úteis
- [Guia de instalação do WordPress no Ubuntu](https://ubuntu.com/tutorials/install-and-configure-wordpress)
- [Documentação: Módulo mysql_db](https://docs.ansible.com/ansible/latest/collections/community/mysql/mysql_db_module.html)
- [Documentação: Módulo mysql_user](https://docs.ansible.com/ansible/latest/collections/community/mysql/mysql_user_module.html)
- [Documentação: Módulo replace](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/replace_module.html)
- [Documentação: Módulo lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html)
