# Comandos Docker

## Baixar imagens
- `docker pull <imagem>` – Faz o download de uma imagem do Docker Hub.
  - Exemplo: `docker pull ubuntu`

## Listar imagens baixadas
- `docker images` – Exibe todas as imagens disponíveis no host.

## Executar containers
- `docker run -i -t -d -p -P <imagem>` – Cria e acessa um container interativo.
  - `-i` – Mantém a entrada interativa.
  - `-t` – Aloca um terminal.
  - `-d` – Executa o container em segundo plano (detached).
  - `-p <host_port>:<container_port>` – Mapeia portas do container para o host.
  - `-P` – Mapeia automaticamente portas expostas.
  - `--name` - Nomeia o container criado
  - Exemplo: `docker run -itd -p 8080:80 ubuntu`
  - Exemplo: `docker run -it ubuntu`

## Executar comandos em um container em execução
- `docker exec -it <id ou nome> <comando>` – Executa um comando dentro de um container em execução.
  - Exemplo: `docker exec -it 43aac92b4c99 bash` – Acessa o shell de um container.
  - Exemplo: `docker exec -it 43aac92b4c99 ls /app` – Lista os arquivos dentro da pasta `/app` do container.

## Ver portas mapeadas de um container
- `docker port <id ou nome>` – Exibe as portas expostas de um container e seus mapeamentos para a máquina host.
  - Exemplo: `docker port 43aac92b4c99`

## Listar containers em execução
- `docker ps` – Mostra apenas os containers em execução.
- `docker ps -a` – Lista todos os containers, incluindo os finalizados.
- `docker ps -s` – Exibe o tamanho dos containers.
- `docker ps -as` – Lista todos os containers e exibe seus tamanhos.

## Remover containers
- `docker rm <id ou nome>` – Remove um container específico.
  - Exemplo: `docker rm 43aac92b4c99`
- `docker rm $(docker ps -qa)` – Remove todos os containers existentes.

## Iniciar containers
- `docker start <id ou nome>` – Inicia um container parado.
  - Exemplo: `docker start 43aac92b4c99`
- `docker start $(docker ps -aq)` – Inicia todos os containers parados.

## Parar containers
- `docker stop <id ou nome>` – Para um container em execução.
  - Exemplo: `docker stop 43aac92b4c99`
- `docker stop $(docker ps -q)` – Para todos os containers em execução.

## Pausar e retomar containers
- `docker pause <id ou nome>` – Suspende temporariamente a execução de um container.
  - Exemplo: `docker pause 43aac92b4c99`
- `docker unpause <id ou nome>` – Retoma a execução de um container pausado.
  - Exemplo: `docker unpause 43aac92b4c99`

## Remover imagens
- `docker rmi <imagem>` – Remove uma imagem específica.
  - Exemplo: `docker rmi ubuntu`
- `docker rmi <id_da_imagem>` – Remove uma imagem pelo ID.
  - Exemplo: `docker rmi d1b55fd07600`
- `docker image prune -a` – Remove todas as imagens não utilizadas.

## Criar imagens personalizadas
- **Dockerfile** – Arquivo usado para definir como uma imagem é criada.
- `docker build -t <nome>` – Compila uma imagem a partir de um Dockerfile.

## Fazer login no Docker Hub
- `docker login` – Faz login no Docker Hub para enviar ou baixar imagens privadas.
  - Exemplo: `docker login -u meu_usuario`

## Enviar imagens para o Docker Hub
- `docker push <usuario>/<imagem>:<tag>` – Envia uma imagem para um repositório no Docker Hub.
  - Exemplo: `docker push meu_usuario/minha_imagem:latest`

## Criar uma nova tag para uma imagem
- `docker tag <imagem_origem> <usuario>/<nova_imagem>:<tag>` – Atribui uma nova tag a uma imagem existente.
  - Exemplo: `docker tag ubuntu meu_usuario/ubuntu-custom:latest`

## Listar volumes
- `docker volume ls` – Lista todos os volumes existentes no host Docker.

## Criar um volume
- `docker volume create <nome_do_volume>` – Cria um novo volume.
  - Exemplo: `docker volume create meu_volume`

## Inspecionar um volume
- `docker volume inspect <nome_do_volume>` – Exibe informações detalhadas sobre um volume.
  - Exemplo: `docker volume inspect meu_volume`

## Remover um volume
- `docker volume rm <nome_do_volume>` – Remove um volume específico.
  - Exemplo: `docker volume rm meu_volume`

## Remover volumes não utilizados
- `docker volume prune` – Remove todos os volumes não utilizados.
  - Exemplo: `docker volume prune` – Remove volumes que não estão mais em uso.

## Usar um volume ao criar um container
- `docker run -v <nome_do_volume>:<caminho_no_container>` – Monta um volume em um container.
  - Exemplo: `docker run -it -v meu_volume:/app ubuntu` – Monta o volume `meu_volume` na pasta `/app` dentro do container.