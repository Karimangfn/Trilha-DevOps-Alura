# Separando as Máquinas

## Instalação e Configuração

- **Divisão do Playbook**: Separação da rotina em duas partes distintas: uma para o banco de dados (MySQL) e outra para a aplicação (WordPress e Apache).
- **Ajuste de Dependências**: 
    - Remoção de pacotes de banco de dados (`mysql-server`, `python3-pymysql`) da tarefa de instalação da aplicação.
    - Criação de uma tarefa de instalação exclusiva para o banco de dados contendo apenas `mysql-server` e `python3-pymysql`.
- **Configuração de Rede do MySQL**: Alteração do arquivo `/etc/mysql/mysql.conf.d/mysqld.cnf`, substituindo o `bind-address` de `127.0.0.1` por `0.0.0.0` para permitir conexões externas.
- **Handler de Reinicialização**: Implementação de um handler para reiniciar o serviço MySQL sempre que houver alteração nas configurações de bind-address.

---

## Estrutura do Projeto

### Atualização do Inventário
Criação de grupos no arquivo `hosts` para separar os endereços IP por função:

```ini
[wordpress]
192.168.31.76 ansible_user=alura ansible_ssh_private_key_file='/home/alura/ansible/delta1'

[mysql]
192.168.31.75 ansible_user=alura ansible_ssh_private_key_file='/home/alura/ansible/deltadb'
```

### Comandos de Preparação
Geração de chave SSH para a nova máquina de banco de dados e cadastro nos hosts:

```bash
ssh-keygen # Nomear como deltadb
ssh-copy-id -i delta1.pub alura@192.168.31.76
ssh-copy-id -i deltadb.pub alura@192.168.31.75
```

---

## Implementação da Infraestrutura

### Comunicação via Rede
- **Ajuste no wp-config**: Substituição do parâmetro `localhost` pelo endereço IP da máquina `deltadb` (`192.168.31.75`) no arquivo de configuração do WordPress.
- **Permissões de Usuário**: Atualização do módulo `mysql_user` para permitir conexões vindas do IP da aplicação (`192.168.31.76`), além do `localhost` e `127.0.0.1`.

```yaml
- name: Create database user with name 'wordpress_user'
  community.mysql.mysql_user:
    name: 'wordpress_user'
    password: '12345'
    priv: 'wordpress_db.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER'
    state: present
    host: '{{ item }}'
  with_items:
    - 'localhost'
    - '127.0.0.1'
    - '192.168.31.76'
  become: yes
```

### Configuração do Banco de Dados
Uso do módulo `replace` para liberar o acesso remoto no arquivo de configuração do MySQL:

```yaml
- name: configure database
  ansible.builtin.replace:
    path: /etc/mysql/mysql.conf.d/mysqld.cnf
    regexp: "127.0.0.1"
    replace: "0.0.0.0"
  notify:
    - restart mysql
  become: yes
```

---

## Boas Práticas de Infraestrutura

- **Escalabilidade Independente**: Permite dimensionar cada componente (App ou Banco) de forma isolada conforme a carga.
- **Isolamento de Recursos**: Garante que cada parte da aplicação tenha recursos computacionais dedicados, evitando competição.
- **Segurança e Resiliência**: O isolamento entre componentes contribui para a segurança geral; se um componente falhar ou for comprometido, os outros permanecem protegidos.

---

## Links Úteis
- [Guia de instalação do Wordpress no Ubuntu](https://ubuntu.com/tutorials/install-and-configure-wordpress)
- [Documentação do modulo mysql_db](https://docs.ansible.com/ansible/latest/collections/community/mysql/mysql_db_module.html)
- [Documentação do modulo mysql_user](https://docs.ansible.com/ansible/latest/collections/community/mysql/mysql_user_module.html)
