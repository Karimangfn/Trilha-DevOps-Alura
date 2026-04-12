# Instalando o Wordpress

## Instalação e Configuração

- **Criação de Diretórios**: Utilização do módulo `ansible.builtin.file` para criar a pasta `/srv/www`.
- **Gestão de Permissões**: Definição de proprietário (`owner`) e grupo (`group`) como `www-data` para garantir que o servidor web tenha acesso aos arquivos.
- **Uso do Become**: Necessidade de `become: yes` para executar tarefas que exigem privilégios de superusuário (`sudo`).

## Estrutura do Projeto

- **Arquivos e Pastas**:
  - `provisioning.yml`: Playbook principal contendo as tarefas de automação.
  - `files/`: Diretório local utilizado para armazenar arquivos de configuração que serão enviados ao servidor.
  - `files/wordpress.conf`: Arquivo de configuração do VirtualHost para o Apache.

## Implementação da Infraestrutura

### Download e Descompactação

- Utilização do módulo `ansible.builtin.unarchive` para baixar e extrair o WordPress em um único passo.
- **Parâmetro `remote_src: yes`**: Indica que o download e a extração devem ocorrer diretamente na máquina de destino, evitando o tráfego desnecessário entre o controlador Ansible e o nó remoto.

### Configuração do Apache

- Uso do módulo `ansible.builtin.copy` para transferir o arquivo `wordpress.conf` da máquina local para `/etc/apache2/sites-available/000-default.conf`.
- A substituição do arquivo padrão garante que o Apache direcione o tráfego para a instalação do WordPress em `/srv/www/wordpress`.

### Automação com Handlers

- **Notificações**: Uso da diretiva `notify` em tarefas de cópia de configuração para disparar ações apenas quando houver mudanças (`changed`).
- **Manipuladores (Handlers)**: Implementação do handler `restart apache` utilizando o módulo `service` para reiniciar o serviço `apache2`.
- **Vantagem**: Evita reinicializações desnecessárias do serviço caso os arquivos de configuração não tenham sofrido alterações, garantindo a idempotência do playbook.

## Conceitos e Boas Práticas

- **Partes de uma Aplicação**: Compreensão da divisão entre aplicação (lógica/arquivos), servidor web (Apache/Nginx) e banco de dados (MySQL/MariaDB).
- **Idempotência**: O Ansible verifica o estado atual antes de agir (ex: não recria diretórios ou copia arquivos idênticos).
- **Handlers vs Shell**: Preferência pelo uso de módulos nativos e handlers em vez de comandos `shell` diretos para maior segurança e controle de estado.

## Links Úteis
- [Guia de instalação do WordPress no Ubuntu](https://ubuntu.com/tutorials/install-and-configure-wordpress)
- [Documentação: Módulo file](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html)
- [Documentação: Módulo unarchive](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/unarchive_module.html)
- [Documentação: Módulo copy](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html)
- [Lista de módulos Builtin do Ansible](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)
