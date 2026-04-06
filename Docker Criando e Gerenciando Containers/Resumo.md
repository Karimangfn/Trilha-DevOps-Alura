# Resumo sobre Docker

## Ambientes de Desenvolvimento
- Desenvolvimento de software geralmente envolve múltiplos ambientes:
  - Desenvolvimento
  - Teste
  - Homologação
  - Produção
- Diferenças entre ambientes podem causar problemas de compatibilidade.
- O Docker ajuda a minimizar essas divergências.

## O que é o Docker?
- Sistema de virtualização não convencional.
- Diferente de máquinas virtuais tradicionais (ex.: VirtualBox, VMWare), que precisam de uma instalação completa do S.O.
- Utiliza containers, que compartilham o kernel do sistema operacional.

## Conceito de Containers
- Inspirado nos containers de transporte marítimo.
- Proporciona isolamento e portabilidade para aplicações.
- Facilita a movimentação de software entre ambientes sem depender de configurações externas.

## Vantagens do Docker
- Mantém o ambiente de execução uniforme entre desenvolvimento e produção.
- Elimina problemas com pré-requisitos de software.
- Pode conter todos os serviços dependentes em containers separados.
- Reduz divergências entre ambientes.

## Como o Docker funciona internamente?
- Inicialmente utilizava LXC (Linux Containers).
- Baseado em funcionalidades do kernel Linux:
  - **chroot**: Mapeamento de diretórios.
  - **cgroup**: Controle de recursos como CPU e memória.
  - **kernel namespace**: Isolamento de processos.
  - **kernel capabilities**: Permite executar comandos privilegiados.

## Instalação do Docker
- Duas versões disponíveis:
  - **Docker Community Edition (CE)** – Versão gratuita para desenvolvedores.
  - **Docker Enterprise Edition (EE)** – Versão paga, com suporte empresarial.
- Após instalação, validar com `docker --version`.

## Imagens no Docker
- Docker trabalha com imagens, que servem como base para containers.
- Imagens podem ser baixadas de um repositório chamado **registry**.
- Repositórios podem conter imagens oficiais ou personalizadas.

## Containers e Imagens
- **Imagens** são modelos prontos para containers.
- **Containers** são instâncias de imagens em execução.