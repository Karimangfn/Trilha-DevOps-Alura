# Nome da imagem : versão dela. As versões disponiveis podem ser vistas nas Tags
FROM node:23
# Definir nosso diretorio padrão
WORKDIR /app-node
# Nos mostra em qual porta vai rodar a aplicação (Não é obrigatorio)
EXPOSE 3000
# Copio todos os arquivos da minha aplicação para uma pasta padrão dentro do container, é possivel tambem colocar o nome da pasta, depois do primeiro ponto
COPY . .
# Instalo as dependencias
RUN npm install
# Executo a aplicação
ENTRYPOINT npm start