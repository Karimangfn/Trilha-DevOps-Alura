# Dependências com Ansible

## Instalação e Configuração

- **Gerenciamento de Pacotes (`apt`)**: O Ansible utiliza o módulo `apt` para gerenciar pacotes em distribuições baseadas em Debian/Ubuntu.
  - **`pkg`**: Lista de pacotes a serem instalados (ex: `python3`, `virtualenv`).
  - **`update_cache: yes`**: Equivalente ao `apt-update`, garante que a lista de repositórios esteja atualizada antes da instalação.
- **Privilégios de Superusuário (`become`)**: Necessário para tarefas que exigem permissão de administrador (`sudo`). No Ansible, utiliza-se `become: yes`.
- **Ambientes Virtuais (`pip`)**: O módulo `pip` do Ansible gerencia dependências Python de forma isolada.
  - **`virtualenv`**: Define o caminho onde o ambiente virtual será criado/ativado (ex: `/home/ubuntu/tcc/venv`).
  - **`name`**: Lista de bibliotecas para instalação (ex: `django`, `djangorestframework`).

## Estrutura do Projeto

- **Organização de Pastas**: O Ansible automatiza a criação de diretórios de projeto (ex: `/home/ubuntu/tcc/`) e a estrutura interna do ambiente virtual.
- **Ciclo de Vida**: Recomenda-se o uso de `terraform destroy` seguido de `terraform apply` ao realizar mudanças estruturais drásticas na máquina para garantir um ambiente limpo, embora o Ansible permita a atualização incremental via SSH.

## Desenvolvimento da Infraestrutura e Aplicação

### Automação de Dependências

O arquivo `playbook.yml` centraliza a instalação do runtime e das bibliotecas:

```yaml
- name: Instalando o python3, virtualenv
  apt:
    pkg:
    - python3
    - virtualenv
    update_cache: yes
  become: yes

- name: Instalando dependencias com pip (Django e Django Rest)
  pip:
    virtualenv: /home/ubuntu/tcc/venv
    name:
    - django
    - djangorestframework
```

### Inicialização do Framework (Django)

Após o provisionamento e configuração via Ansible, o deploy manual/teste do framework envolve:
1. **Ativação da Venv**: `. venv/bin/activate`
2. **Criação do Projeto**: `django-admin startproject setup .`
3. **Execução do Servidor**: `python manage.py runserver 0.0.0.0:8000` (O IP `0.0.0.0` permite que o servidor escute requisições externas).

### Ajustes de Segurança da Aplicação

- **`ALLOWED_HOSTS`**: No arquivo `settings.py` do Django, é necessário configurar as permissões de host para aceitar requisições do IP público da AWS.
  - Configuração de desenvolvimento: `ALLOWED_HOSTS = ['*']` (permite qualquer host).

## Boas Práticas e Fundamentos

- **Isolamento**: O uso de `virtualenv` evita conflitos entre dependências globais do sistema e dependências específicas do projeto.
- **Reprodutibilidade**: Garantir que a versão da linguagem (`python3 --version`) e das bibliotecas (`pip freeze`) sejam consistentes em qualquer máquina provisionada.
- **Agilidade no Desenvolvimento**: A combinação de Terraform (Infra) e Ansible (Config) permite que um novo desenvolvedor tenha um ambiente pronto para uso em minutos, com todas as linguagens e frameworks pré-configurados.



## Links Úteis:
- [Ansible APT Module Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)
- [Ansible PIP Module Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html)
- [Django Documentation - Settings (Allowed Hosts)](https://docs.djangoproject.com/en/stable/ref/settings/#allowed-hosts)
