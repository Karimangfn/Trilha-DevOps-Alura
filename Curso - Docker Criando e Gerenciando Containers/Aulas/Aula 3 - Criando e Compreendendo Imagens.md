# Imagens e Containers no Docker

## Conceito de Imagens e Containers  
Imagens no Docker podem ser comparadas a receitas de bolo, contendo todas as instruções para criar containers. Containers são instâncias dessas imagens, podendo ser modificados sem alterar a base original. Cada container possui uma camada de leitura e escrita, permitindo ajustes temporários sem modificar a imagem principal.

## Criação de Imagens com Dockerfile  
Criamos imagens personalizadas utilizando um **Dockerfile**, que define:  
- A imagem base com `FROM node:14`.  
- O diretório de trabalho com `WORKDIR /app-node`.  
- A cópia dos arquivos do projeto com `COPY . .`.  
- A instalação de dependências com `RUN npm install`.  
- O comando de execução do container com `ENTRYPOINT npm start`.  

Após criar o **Dockerfile**, usamos `docker build -t nome-da-imagem` para construir a imagem e `docker run -d -p host:container nome-da-imagem` para executar um container a partir dela.

## Melhorando a Configuração das Imagens  
Exploramos três melhorias no Dockerfile:  
- **Documentação de portas** com `EXPOSE`, indicando a porta utilizada pela aplicação.  
- **Uso de variáveis de ambiente** com `ARG` e `ENV`, permitindo parametrização sem alterar o código.  
- **Diferença entre `ARG` e `ENV`**:  
  - `ARG` é usado apenas na construção da imagem.  
  - `ENV` é persistente e utilizado na execução do container.  

## Publicação de Imagens no Docker Hub  
Para compartilhar imagens, seguimos os passos:  
- Autenticação com `docker login`.  
- Garantia de que a imagem pertence ao usuário, ajustando com `docker tag` se necessário.  
- Upload com `docker push`.  

O Docker Hub reutiliza camadas existentes, tornando o envio mais eficiente. Além disso, podemos manter múltiplas versões das imagens utilizando tags.

Documentação DockerFile: https://docs.docker.com/reference/dockerfile/

