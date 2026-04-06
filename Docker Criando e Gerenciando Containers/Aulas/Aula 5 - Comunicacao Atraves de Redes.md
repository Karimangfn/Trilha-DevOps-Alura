# Redes e Comunicação entre Contêineres no Docker

## Comunicação via Rede Bridge  
Os contêineres do Docker são isolados por padrão, mas podem se comunicar se estiverem na mesma rede. O Docker cria automaticamente uma rede chamada **bridge**, onde os contêineres podem interagir.  
- **Comandos úteis**: `docker ps` e `docker inspect` para verificar redes e configurações.  
- **Problema com IPs**: Como os endereços IP podem mudar ao reiniciar os contêineres, recomenda-se o uso de **DNS ou hostnames** para uma comunicação mais estável.  

## Redes Personalizadas para Comunicação Estável  
Criamos redes personalizadas para garantir a comunicação previsível entre contêineres.  
- Criamos uma rede bridge personalizada com:  
  ```bash
  docker network create --driver bridge nome-da-rede
  ```  
- Ao iniciar os contêineres, utilizamos a flag `--network` para conectá-los à rede criada.  
- Nomeamos os contêineres com `--name`, permitindo que se comuniquem via **hostname** em vez de IPs.  
- Testamos a comunicação entre dois contêineres utilizando `ping`, confirmando que a abordagem funciona corretamente.  

## Redes None e Host  
O Docker oferece dois modos de rede específicos para casos distintos:  
- **Rede none**: O contêiner não possui interface de rede e fica completamente isolado.  
- **Rede host**: O contêiner compartilha a interface de rede do host, eliminando a necessidade de mapeamento de portas.  
  - **Vantagem**: Redução de sobrecarga de rede.  
  - **Desvantagem**: Pode gerar conflitos de portas com outras aplicações no host.  

## Comunicação entre Aplicação e Banco de Dados  
Criamos um ambiente com uma aplicação Node.js conectada a um banco de dados MongoDB utilizando a rede personalizada **minha-bridge**.  
1. **Baixamos as imagens** necessárias:  
   ```bash
   docker pull mongo:4.4.6
   docker pull aluradocker/alura-books:1.0
   ```  
2. **Criamos o contêiner do MongoDB** na rede `minha-bridge`:  
   ```bash
   docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6
   ```  
3. **Iniciamos a aplicação** na mesma rede e mapeamos a porta 3000:  
   ```bash
   docker run -d --network minha-bridge -p 3000:3000 --name minha-app aluradocker/alura-books:1.0
   ```  
4. A aplicação pode acessar o banco de dados utilizando o hostname `meu-mongo` dentro da rede.  

Essa abordagem garante uma comunicação confiável e facilita a configuração de ambientes isolados e organizados no Docker.

