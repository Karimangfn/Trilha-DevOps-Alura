# Usando Playbook e Dependencias

## Criando Playbook

* Evita uso de comandos diretos no terminal.
* Permite replicação e documentação das tarefas.
* Arquivo com extensão `.yml`.
* Nome pode ser qualquer um (ex: provisioning.yml).

### Estrutura básica

```id="r1l9yq"
---
- hosts: all
  tasks:
    - shell: 'echo ola, mundo'
```

* `---` define início do arquivo YAML.
* `hosts` define onde executar.
* `tasks` define as tarefas.
* Indentação é obrigatória e essencial.

## Executando Playbook

```id="g8r7i2"
ansible-playbook provisioning.yml -i hosts
```

* Executa tarefas definidas no playbook.
* Executa automaticamente:

  * Gathering Facts
  * Tasks definidas

## Instalando dependências do WordPress

* Aplicação composta por:

  * Servidor web (Apache)
  * Aplicação (WordPress)
  * Banco de dados

## Usando módulo apt

```id="u3zv1k"
---
- hosts: all
  tasks:
    - name: Install apache httpd 
      ansible.builtin.apt:
        name: apache2
        state: present
```

* Módulo: `ansible.builtin.apt`
* `state` define estado do pacote:

  * present
  * latest
  * absent
  * outros

## Atualizando repositórios

```id="k2l6zx"
update_cache: yes
```

## Instalando versão mais recente

```id="r7m2hf"
state: latest
```

## Instalando múltiplos pacotes

```id="b4k9pq"
---
- hosts: all
  tasks:
    - name: Install dependencias 
      ansible.builtin.apt:
        pkg: 
          - apache2
          - ghostscript
          - libapache2-mod-php
          - mysql-server
          - php
          - php-bcmath
          - php-curl
          - php-imagick
          - php-intl
          - php-json
          - php-mbstring
          - php-mysql
          - php-xml
          - php-zip
        state: latest
        update_cache: yes
```

* Uso de `pkg` permite instalar lista de pacotes.
* Evita múltiplas tasks repetidas.

## Permissão com sudo

```id="x5n8ws"
become: yes
```

* Necessário para executar comandos administrativos.

## Executando com sudo

```id="p0v6dn"
ansible-playbook provisioning.yml -i hosts -K
```

* `-K` solicita senha sudo.

## Para saber mais: liberando acesso sem senha de SUDO

### Método manual

```id="z2t4ay"
usuario ALL=(ALL) NOPASSWD: ALL
```

### Método com Ansible

```id="l8q3rm"
---
- hosts: all
  tasks:
    - shell:  'echo "{{ansible_user}} ALL=(ALL) NOPASSWD: ALL" > etc/sudoers.d/{{ansible_user}}'
      become: yes
```

* Não recomendado para ambientes expostos.

## Uso de loops com pkg

* `pkg` já funciona como loop para múltiplos pacotes.
* Evita repetição de código.

## Para saber mais: outros métodos de loops

```id="c7w1bx"
- name: 'Instala as dependências’
  ansible.builtin.apt:
    pkg:
      - apache2 
      - ghostscript 
    state: latest
    update_cache: yes
    become: yes
```

* Alternativa:

  * `with_items`
  * `{{ item }}`
* Menos utilizado atualmente.

## Observações

* Indentação é essencial no YAML.
* Uso de listas melhora manutenção.
* Uso de `become` necessário para permissões.
* Playbooks permitem automação e replicação de tarefas.
