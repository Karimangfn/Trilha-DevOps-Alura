# Primeiros Passos com Prometheus

## Estrutura da Stack e Redes Docker

- A aplicação utiliza um ambiente totalmente conteinerizado para garantir isolamento.
- **Divisão de Redes**:
  - `database`, `cache`, `api`: Redes internas para comunicação entre containers.
  - `monit`: Exposição da interface do Prometheus (porta 9090).
  - `proxy`: Porta de entrada (80) via Nginx.
- **Isolamento**: Uso de `expose` em vez de `ports` para que MySQL (3306) e Redis (6379) não fiquem expostos fora do Docker.

## Configuração do Prometheus (`prometheus.yml`)

- Define a estratégia de coleta (*scraping*) de métricas.
- **Configuração de Jobs**:
  - `prometheus-forum-api`: Monitora o próprio Prometheus.
  - `app-forum-api`: Monitora a aplicação no path `/actuator/prometheus`.

```yaml
# Trecho do prometheus.yml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: prometheus-forum-api
    scrape_interval: 15s
    static_configs:
      - targets: ['prometheus-forum-api:9090']

  - job_name: app-forum-api
    metrics_path: /actuator/prometheus
    static_configs:
      - targets: ['app-forum-api:8080']
```

## Simulação de Tráfego (Client Sintético)

Para alimentar as métricas, utiliza-se um container que simula requisições constantes.

### Dockerfile do Client
```dockerfile
FROM debian
USER root
COPY ./client.sh /scripts/client.sh
RUN apt update && apt install curl -y && chmod +x /scripts/client.sh
ENTRYPOINT ["/scripts/client.sh"]
```

### Script de Automação (`client.sh`)
O script utiliza um laço `while true` e a variável `$RANDOM` para distribuir as requisições:
- **55%** para `/topicos`.
- **30%** para IDs aleatórios (1 a 3).
- **10%** Autenticação com sucesso.
- **5%** Erros de login e 404.

```bash
#!/bin/bash
HOST='proxy-forum-api'
while true
    do
    ENDP=`expr $RANDOM % 3 + 1`
    NUMB=`expr $RANDOM % 100 + 1`
    
    if [ $NUMB -le 55 ]; then
        curl --silent --output /dev/null http://${HOST}/topicos
    elif [ $NUMB -ge 56 ] && [ $NUMB -le 85 ] ; then
        curl --silent --output /dev/null http://${HOST}/topicos/$ENDP
    elif [ $NUMB -ge 86 ] && [ $NUMB -le 95 ] ; then
        curl --silent --output /dev/null --data '{"email":"moderador@email.com","senha":"123456"}' \
         --header "Content-Type:application/json" --request POST http://${HOST}/auth
    else
        curl --silent --output /dev/null http://${HOST}/topicos/0
    fi
    sleep 0.75
done
```

## Interface Web do Prometheus

- Acessível em `http://localhost:9090`.
- **Status > Targets**: Verifica se os alvos (API e o próprio Prometheus) estão "UP".
- **Status > Configuration**: Valida se o arquivo `.yml` foi lido corretamente.
- **Graph**: Local para realizar consultas em PromQL.

## Gerenciamento via Docker Compose

- **Dependências**: A ordem de subida é fundamental: `Redis -> MySQL -> App -> Proxy -> Prometheus -> Client`.
- **Persistência**: O volume `./prometheus/prometheus_data` armazena o banco TSDB.
- **Comando de Subida**:
```bash
docker-compose up -d
```

## Observações Técnicas Importantes

- **Permissões**: O diretório de dados do Prometheus no host precisa de `chmod 777 prometheus_data` devido ao usuário interno do container.
- **Healthcheck**: O container da aplicação possui um teste de saúde via `curl` no endpoint `/actuator/health` antes de liberar os serviços dependentes.
