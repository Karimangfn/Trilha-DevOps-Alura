# Criando Maquinas Virtuais

## O que aprenderemos

* Criação e configuração de máquinas virtuais.
* Uso de playbooks para gerenciar aplicações.
* Automação de uma aplicação de três camadas:

  * WordPress (aplicação)
  * Apache (servidor web)
  * MySQL (banco de dados)
* Uso de variáveis e modularização.
* Utilização de templates de configuração.

## Pré-requisitos

* Conhecimento básico de:

  * Linux
  * SSH
  * Redes (portas e IP)
  * Infraestrutura e máquinas virtuais

## DevOps e Infraestrutura como Código

* DevOps integra desenvolvimento (Dev) e operações (Ops).
* Objetivo:

  * Reduzir tempo de feedback
  * Automatizar processos
* Antes:

  * Scripts manuais
  * Configurações feitas diretamente nos servidores
* Problema:

  * Dificuldade de manter consistência entre servidores

## Ansible

* Ferramenta para garantir o estado dos servidores.
* Baseado em Python.
* Utiliza:

  * SSH
  * Inventário
  * Playbooks
  * Roles

### Características

* Sintaxe baseada em YAML
* Abordagem declarativa
* Agentless (não precisa instalar agentes)
* Funciona via SSH
* Compatível com múltiplos sistemas

### Vantagens

* Simplicidade e legibilidade
* Infraestrutura como código (IaC)
* Ampla compatibilidade
* Reutilização com roles
* Orquestração de múltiplos servidores
* Segurança via SSH
* Comunidade ativa
* Integração com outras ferramentas
* Gratuito e open source

## Criação do Ambiente

* Ansible não funciona nativamente no Windows.
* Necessário usar máquina virtual Linux.
* Ferramenta utilizada: VirtualBox.

## Instalação do VirtualBox

* Baixar versão para Windows.
* Executar instalador:

  * Next → Next → Yes → Yes → Install → Finish

## Criação da Máquina Virtual

* Menu: Máquina → Novo
* Nome: Ansible
* Selecionar ISO do Ubuntu Server
* Pular instalação desassistida
* Manter configurações padrão:

  * Hardware
  * Disco (25GB)
* Finalizar criação

## Configuração da Máquina

* Configurações → Rede
* Alterar para modo Bridge

## Instalação do Ubuntu Server

* Selecionar idioma Português
* Layout: Portuguese - Brazil
* Instalar Ubuntu Server
* Manter configurações padrão de:

  * Rede
  * Proxy
  * Disco
* Criar usuário:

  * Nome do servidor: Ansible
  * Usuário: Alura
  * Definir senha
* Instalar OpenSSH
* Finalizar instalação e reiniciar

## Segunda Máquina Virtual

* Criar segunda VM (Delta 1)
* Mesma configuração da primeira
* Importante:

  * Usar modo Bridge
* Objetivo:

  * Separar ambiente de controle e aplicação

## Uso do VS Code com SSH

* Instalar extensão: Remote - SSH
* Conectar via:

  * usuario@endereço_ip
* Descobrir IP com:

```
ip addr
```

## Preparação do Ambiente

* Criar pasta:

```
mkdir ansible
```

* Abrir pasta no VS Code

## Criação de Chave SSH

```
ssh-keygen
```

* Nome da chave: delta1
* Sem senha

## Envio da Chave

```
ssh-copy-id -i delta1.pub alura@192.168.31.70
```

## Instalação do Ansible

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository-yes-update ppa:ansible/ansible
sudo apt install ansible
```

## Arquivo de Inventário (hosts)

```
[wordpress]
192.168.31.70
```

### Exemplo com múltiplos grupos

```
[wordpress]
192.168.31.70

[grupo1]
m1
m2
m3
m4
```

## Execução de Comando com Ansible

```
ansible wordpress -u alura --private-key delta1 -i hosts -m shell -a 'echo ola, mundo'
```

## Simplificação do Comando

### Atualização do arquivo hosts

```
[wordpress]
192.168.31.70 ansible_user=alura ansible_ssh_private_key_file='/home/alura/ansible/delta1'
```

### Novo comando

```
ansible wordpress -i hosts -m shell -a 'echo ola, mundo'
```

## Resultado Esperado

```
192.168.31.70 | CHANGED |
ola, mundo
```

## Observações

* Uso de aspas no caminho evita problemas com espaços.
* Centralizar configurações no hosts simplifica comandos.
* Permite diferentes usuários e chaves por máquina.
