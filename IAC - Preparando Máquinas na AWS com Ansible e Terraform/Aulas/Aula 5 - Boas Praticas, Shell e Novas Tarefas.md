# Boas Práticas, Shell e Novas Tarefas

## Instalação e Configuração

- **Comandos Shell no Ansible**: O módulo `shell` permite a execução de comandos Bash diretamente na instância remota. Diferente de outros módulos, ele é útil para ferramentas que não possuem módulos nativos do Ansible (ex: `django-admin`).
  - **Encadeamento de Comandos**: O Ansible abre um novo shell para cada tarefa. Para manter o estado (como ativar uma `venv` e executar um comando logo após), deve-se usar o ponto e vírgula (`;`) na mesma linha de comando.
- **Edição de Arquivos (`lineinfile`)**: Módulo utilizado para encontrar, alterar ou remover linhas específicas dentro de arquivos de texto.
  - **`regexp`**: Localiza a linha via Expressão Regular.
  - **`line`**: Define como a linha deve ficar após a alteração.
  - **`backrefs: yes`**: Garante que a alteração só ocorra se a expressão regular for encontrada, evitando duplicidade ou inserções indesejadas.

## Estrutura do Projeto

- **Automação de Inicialização**: A tarefa de "Iniciando o projeto" automatiza a criação da estrutura de pastas do framework dentro do diretório de trabalho (`/home/ubuntu/tcc/`).
- **Tratamento de Erros**: O parâmetro `ignore_errors: yes` pode ser adicionado a tarefas que falham se executadas repetidamente (como criar um projeto que já existe), permitindo que o Playbook prossiga.



## Desenvolvimento da Infraestrutura e Aplicação

### Automação de Comandos Bash

Trecho do `playbook.yml` para inicializar o projeto Django:
```yaml
- name: Iniciando o projeto
  shell: '. /home/ubuntu/tcc/venv/bin/activate; django-admin startproject setup /home/ubuntu/tcc/'
  ignore_errors: yes
```

### Configuração Automática de Código

Para liberar o acesso à aplicação sem intervenção manual no servidor:
```yaml
- name: Alterando os hosts do settings
  lineinfile:
    path: /home/ubuntu/tcc/setup/settings.py
    regexp: 'ALLOWED_HOSTS'
    line: 'ALLOWED_HOSTS = ["*"]'
    backrefs: yes
```

## Boas Práticas e Fundamentos

- **Idempotência vs. Shell**: O módulo `shell` geralmente não é idempotente por padrão (ele executa toda vez). Por isso, é importante usar `ignore_errors` ou mecanismos de verificação para evitar que o Playbook pare ao encontrar arquivos já existentes.
- **Caminhos Absolutos**: Sempre utilize caminhos completos (ex: `/home/ubuntu/...`) nos Playbooks para garantir que o Ansible localize os arquivos corretamente, independentemente de onde o shell for iniciado.
- **Ambiente de Desenvolvimento "Ready-to-Go"**: Com essa configuração, o ambiente é entregue ao desenvolvedor não apenas com as bibliotecas instaladas, mas com o projeto iniciado e as permissões de rede (hosts) já configuradas.

## Links Úteis:
- [Ansible Documentation - Shell Module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html)
- [Ansible Documentation - Lineinfile Module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html)
- [RegExr - Testador de Expressões Regulares](https://regexr.com/)
