# Comandos Docker Compose

## Iniciar e parar containers com Docker Compose
- `docker-compose up` – Inicia os serviços definidos no arquivo `docker-compose.yml`.
  - Exemplo: `docker-compose up`
- `docker-compose up -d` – Inicia os serviços em segundo plano (modo detached).
  - Exemplo: `docker-compose up -d`
- `docker-compose down` – Para e remove todos os containers, redes e volumes criados pelo `docker-compose up`.
  - Exemplo: `docker-compose down`
- `docker-compose stop` – Para os serviços sem remover os containers.
  - Exemplo: `docker-compose stop`
- `docker-compose restart` – Reinicia todos os serviços definidos no `docker-compose.yml`.
  - Exemplo: `docker-compose restart`
- `docker-compose pause` – Pausa a execução dos containers.
  - Exemplo: `docker-compose pause`
- `docker-compose unpause` – Retoma a execução dos containers pausados.
  - Exemplo: `docker-compose unpause`

## Listar e verificar status dos containers
- `docker-compose ps` – Lista os containers gerenciados pelo `docker-compose`.
  - Exemplo: `docker-compose ps`
- `docker-compose logs` – Exibe os logs dos containers em execução.
  - Exemplo: `docker-compose logs`
- `docker-compose logs -f` – Exibe os logs em tempo real (seguindo as atualizações).
  - Exemplo: `docker-compose logs -f`
- `docker-compose top` – Exibe os processos em execução dentro dos containers.
  - Exemplo: `docker-compose top`

## Criar, remover e reconstruir serviços
- `docker-compose build` – Constrói ou reconstrói as imagens especificadas no `docker-compose.yml`.
  - Exemplo: `docker-compose build`
- `docker-compose build --no-cache` – Constrói as imagens sem usar cache.
  - Exemplo: `docker-compose build --no-cache`
- `docker-compose rm` – Remove os containers definidos no `docker-compose.yml`.
  - Exemplo: `docker-compose rm`
- `docker-compose rm -f` – Remove os containers sem pedir confirmação.
  - Exemplo: `docker-compose rm -f`
- `docker-compose pull` – Baixa as imagens definidas no `docker-compose.yml`.
  - Exemplo: `docker-compose pull`
- `docker-compose push` – Envia as imagens criadas para um repositório remoto.
  - Exemplo: `docker-compose push`

## Executar comandos dentro dos containers
- `docker-compose exec <serviço> <comando>` – Executa um comando dentro de um serviço em execução.
  - Exemplo: `docker-compose exec alurabooks ls /app`
  - Exemplo: `docker-compose exec meu-mongo mongo`
- `docker-compose run <serviço> <comando>` – Executa um comando em um novo container baseado no serviço especificado.
  - Exemplo: `docker-compose run alurabooks bash`

## Trabalhando com variáveis de ambiente
- `docker-compose --env-file <arquivo>` – Especifica um arquivo de variáveis de ambiente para o `docker-compose`.
  - Exemplo: `docker-compose --env-file .env up -d`
- O `docker-compose.yml` pode utilizar variáveis de ambiente do sistema ou do arquivo `.env` com `${VARIAVEL}`.

## Gerenciamento de volumes e redes
- `docker-compose config` – Valida a sintaxe do arquivo `docker-compose.yml`.
  - Exemplo: `docker-compose config`
- `docker-compose config --services` – Lista todos os serviços definidos no `docker-compose.yml`.
  - Exemplo: `docker-compose config --services`
- `docker-compose config --volumes` – Lista todos os volumes definidos no `docker-compose.yml`.
  - Exemplo: `docker-compose config --volumes`
- `docker-compose config --networks` – Lista todas as redes definidas no `docker-compose.yml`.
  - Exemplo: `docker-compose config --networks`
- `docker-compose down -v` – Remove os containers e os volumes criados pelo `docker-compose`.
  - Exemplo: `docker-compose down -v`

## Trabalhando com múltiplos arquivos de configuração
- `docker-compose -f <arquivo1> -f <arquivo2> up` – Especifica múltiplos arquivos de configuração.
  - Exemplo: `docker-compose -f docker-compose.yml -f docker-compose.override.yml up -d`

## Ajustando réplicas e escalabilidade
- `docker-compose up --scale <serviço>=<número>` – Define quantas instâncias de um serviço devem ser executadas.
  - Exemplo: `docker-compose up --scale alurabooks=3`
- **Obs:** O `scale` só funciona com serviços que não possuem `depends_on` configurado.

## Removendo containers e recursos órfãos
- `docker-compose down --remove-orphans` – Remove containers órfãos (containers criados fora do `docker-compose.yml`).
  - Exemplo: `docker-compose down --remove-orphans`