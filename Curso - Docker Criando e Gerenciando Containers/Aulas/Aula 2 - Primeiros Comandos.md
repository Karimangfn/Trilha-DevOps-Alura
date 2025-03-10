# Execução e Gerenciamento de Containers no Docker

## Comando `docker run` e Execução de Imagens
Exploramos o comando `docker run`, que busca e executa imagens do Docker Hub. Vimos que ao executar `docker run hello-world`, o Docker baixa a imagem caso não a encontre localmente. Também discutimos erros ao usar nomes inválidos e a importância do Docker Hub.

## Criação e Execução de Containers
Ao rodar `docker run ubuntu`, percebemos que o container encerra rapidamente sem um processo ativo. Para evitar isso, podemos usar `sleep`. Aprendemos a diferenciar containers em execução e parados com `docker ps` e `docker ps -a`.

## Gerenciamento de Containers
Vimos como listar (`docker ps -a`), parar (`docker stop`), reiniciar (`docker start`), acessar (`docker exec -it`), pausar (`docker pause`), e remover (`docker rm`) containers. Discutimos a efemeridade dos dados dentro de um container e a importância de medidas de persistência.

## Mapeamento de Portas
Executamos uma aplicação web com `dockersamples/static-site` e usamos `-d` para rodar em segundo plano. Aprendemos a expor portas com `-P` e a mapear portas específicas com `-p`, verificando com `docker port`. Isso facilita o acesso às aplicações em containers.