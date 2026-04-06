# Persistência de Dados no Docker

## Remoção de Containers e Imagens
Containers e imagens que estavam parados podem ser removidos utilizando os comandos:
- `docker container rm`
- `docker rmi`

Com a opção `--force`, é possível forçar a remoção de containers e imagens quando necessário.

## Tamanho dos Containers
O tamanho virtual de um container é igual ao tamanho da imagem, mas o tamanho real começa em zero e aumenta à medida que operações são realizadas dentro do container.

### Bind Mounts
Bind mounts permitem vincular um diretório do sistema de arquivos do host a um diretório dentro do container, garantindo que os dados persistam mesmo após o container ser parado ou removido. A criação de um bind mount é feita utilizando a flag `-v` no comando `docker run`.

## Volumes
Volumes são áreas gerenciadas pelo Docker dentro do sistema de arquivos, recomendados para ambientes produtivos devido à sua segurança e eficiência. Para criar e usar volumes, utiliza-se o comando:
`docker volume create`

## tmpfs
O tmpfs oferece armazenamento temporário que funciona apenas em hosts Linux, armazenando dados na memória, ao invés de na camada de read-write do container. Usando a flag `--tmpfs` ou `--mount` com o tipo `tmpfs`, cria-se um diretório temporário dentro do container, cujos dados são perdidos quando o container é encerrado. Isso é útil para armazenar dados sensíveis, como arquivos de senha, que não devem ser persistidos.

## Comparação entre Métodos de Persistência
- **tmpfs**: Ideal para dados temporários ou sensíveis.
- **Bind Mounts**: Útil para vincular diretórios específicos do host ao container.
- **Volumes**: A solução mais robusta e segura para persistência de dados em ambientes produtivos.