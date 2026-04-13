# Terraform e Ansible

## Instalação e Configuração

- **User Data (Terraform)**: Recurso do Terraform para executar scripts de shell automaticamente no momento em que a instância é provisionada.
- **Ansible**: Ferramenta de gerenciamento de configuração que permite alterar o estado da máquina sem destruí-la.
  - **Inventário (`hosts.yml`)**: Arquivo onde se define os endereços IP (ou domínios) das máquinas que o Ansible irá gerenciar.
  - **Playbook (`playbook.yml`)**: Arquivo de configuração onde são definidas as tarefas (*tasks*) a serem executadas.
- **Conexão**: O Ansible utiliza o protocolo SSH para se comunicar com as instâncias, exigindo a chave privada (`.pem`) e a definição do usuário remoto (ex: `ubuntu`).

## Estrutura do Projeto

- **Arquivos Adicionados**:
  - `hosts.yml`: Mapeia os grupos de servidores e seus respectivos IPs.
  - `playbook.yml`: Contém a lista de instruções para o Ansible.
- **Modificações no `main.tf`**:
  - Adição do bloco `user_data` utilizando a sintaxe `<<-EOF` para scripts Bash integrados.

## Desenvolvimento da Infraestrutura e Configuração

### Automação via Terraform (`user_data`)

- O script Bash dentro do `user_data` automatiza tarefas iniciais:
  ```bash
  #!/bin/bash
  cd /home/ubuntu
  echo "<h1>Feito com Terraform</h1>" > index.html
  nohup busybox httpd -f -p 8080 &
  ```
- **Limitação**: Qualquer alteração no script `user_data` força o Terraform a destruir e recriar a instância do zero (*Replace*).

### Gerenciamento via Ansible

- **Tasks (Tarefas)**: O Ansible divide as ações em blocos nomeados para facilitar o rastreamento.
  - `copy`: Módulo para criar arquivos com conteúdo específico ou copiar arquivos locais para o servidor.
  - `shell`: Módulo para executar comandos de terminal diretamente na instância.
- **Execução do Playbook**:
  ```bash
  ansible-playbook playbook.yml -u ubuntu --private-key chave.pem -i hosts.yml
  ```

## Fundamentos e Boas Práticas

- **Separação de Responsabilidades**: 
  - **Terraform**: Focado no **provisionamento** (criar a "casca" da infraestrutura: servidores, rede, storage).
  - **Ansible**: Focado na **configuração** (instalar pacotes, configurar serviços, gerenciar arquivos internos).
- **Idempotência no Ansible**: O Ansible verifica o estado atual da máquina (*Gathering Facts*). Se uma tarefa já foi realizada (ex: arquivo já existe com o conteúdo correto), ele não a executa novamente, garantindo consistência e velocidade.
- **Manutenibilidade**: Alterar o conteúdo de uma página ou configuração de software via Ansible é mais rápido e seguro do que recriar a infraestrutura inteira via Terraform.

## Links Úteis:
- [Ansible Documentation - Getting Started](https://docs.ansible.com/ansible/latest/getting_started/index.html)
- [Terraform - ec2 user_data](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#user_data)
