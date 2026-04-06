# Orquestração de Contêineres com Docker Compose

## Introdução ao Docker Compose  
O **Docker Compose** é uma ferramenta que permite coordenar vários contêineres dentro de um mesmo ambiente por meio de um único arquivo YAML. Isso facilita a criação e gerenciamento de aplicações complexas que dependem de múltiplos contêineres se comunicando entre si.  
- **Instalação**: O Docker Compose pode ser instalado no Linux e simplifica o processo de subir e parar vários contêineres em relação à execução manual de comandos Docker.  

## Criando um Arquivo docker-compose.yml  
Transformamos os comandos Docker para rodar o Alura Books e o MongoDB em um arquivo **docker-compose.yml**.  

1. Criamos uma pasta chamada **ymls** e abrimos o VS Code nela.  
2. Criamos o arquivo **docker-compose.yml** e definimos a versão:  
   ```yaml
   version: "3.9"
   ```
3. Configuramos os serviços, que representam nossos contêineres:  
   - **MongoDB**:  
     ```yaml
     services:
       mongodb:
         image: mongo:4.4.6
         container_name: meu-mongo
         networks:
           - compose-bridge
     ```
   - **Alura Books**:  
     ```yaml
       alurabooks:
         image: aluradocker/alura-books:1.0
         container_name: alurabooks
         networks:
           - compose-bridge
         ports:
           - "3000:3000"
     ```
4. Criamos a rede personalizada **compose-bridge**:  
   ```yaml
   networks:
     compose-bridge:
       driver: bridge
   ```

## Executando o Docker Compose  
Para subir os contêineres e a rede configurados, utilizamos:  
```bash
docker-compose up
```
Podemos acessar a aplicação pelo navegador em **localhost:3000** e testar o banco de dados em **localhost:3000/seed**.  
Para interromper a execução, usamos:  
```bash
Ctrl + C
```

## Dependências e Controle de Inicialização  
Usamos a diretiva **depends_on** para garantir que um serviço inicie apenas após suas dependências estarem prontas. Isso é útil para garantir que o banco de dados esteja rodando antes da aplicação que o utiliza.  

## Gerenciamento de Contêineres e Logs  
Podemos gerenciar os contêineres e verificar os logs com os seguintes comandos:  
- **Executar em modo detached (em segundo plano):**  
  ```bash
  docker-compose up -d
  ```  
- **Listar os contêineres em execução:**  
  ```bash
  docker-compose ps
  ```  
- **Exibir os logs dos serviços em tempo real:**  
  ```bash
  docker-compose logs -f
  ```  
- **Reiniciar um serviço específico:**  
  ```bash
  docker-compose restart nome-do-servico
  ```  
- **Parar todos os contêineres sem removê-los:**  
  ```bash
  docker-compose stop
  ```  
- **Iniciar contêineres parados sem recriá-los:**  
  ```bash
  docker-compose start
  ```  
- **Encerrar e remover os contêineres e redes criados:**  
  ```bash
  docker-compose down
  ```  

## Recursos Avançados  
A seção **deploy** na documentação do Docker Compose permite configurar o número de réplicas e ajustar o paralelismo, mas essas configurações só funcionam no modo **Swarm** do Docker. Também podemos utilizar **volumes** para persistência de dados, assim como fazemos com redes.