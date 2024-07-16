<p align="center">
<img src="https://cwmkt.com.br/wp-content/uploads/2024/04/logo_github.png" width="240" />
<p align="center">Seja bem-vindo ao Guia de Instalação Docker Evolution API 🚀</p>
</p>
  
<p align="center"> 
<a href="https://hubconnect.top" target="_blank">👉 Participe da Comunidade HubConnect 👈</a>
</p>

<hr />
<hr />

### Caso não tenha Portainer e Traefix instalado, siga primeira etapa

<details>
<summary>Instalando Portainer e Traefix</summary>

### Atualizando Dependências

Atualize os repositórios do Ubuntu executando o seguinte comando:

```bash
sudo apt update && apt upgrade -y
```

----------------------------------------------------------------------------

**Instale o Docker em sua VPS**

```bash
sudo apt install docker.io -y
```

----------------------------------------------------------------------------

**Instalando Portainer**

```bash
docker swarm init
```

```bash
nano traefik.yml
```

```bash
version: "3.8"

services:

  traefik:
    image: traefik:2.11.1
    command:
      - "--api.dashboard=true"
      - "--providers.docker.swarmMode=true"
      - "--providers.docker.endpoint=unix:///var/run/docker.sock"
      - "--providers.docker.exposedbydefault=false"
      - "--providers.docker.network=ecosystem_network"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.web.http.redirections.entryPoint.to=websecure"
      - "--entrypoints.web.http.redirections.entryPoint.scheme=https"
      - "--entrypoints.web.http.redirections.entrypoint.permanent=true"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.letsencryptresolver.acme.httpchallenge=true"
      - "--certificatesresolvers.letsencryptresolver.acme.httpchallenge.entrypoint=web"
      - "--certificatesresolvers.letsencryptresolver.acme.email=contato@seudominio.com.br"
      - "--certificatesresolvers.letsencryptresolver.acme.storage=/etc/traefik/letsencrypt/acme.json"
      - "--log.level=DEBUG"
      - "--log.format=common"
      - "--log.filePath=/var/log/traefik/traefik.log"
      - "--accesslog=true"
      - "--accesslog.filepath=/var/log/traefik/access-log"
    deploy:
      placement:
        constraints:
          - node.role == manager
      labels:
        - "traefik.enable=true"
        - "traefik.http.middlewares.redirect-https.redirectscheme.scheme=https"
        - "traefik.http.middlewares.redirect-https.redirectscheme.permanent=true"
        - "traefik.http.routers.http-catchall.rule=hostregexp(`{host:.+}`)"
        - "traefik.http.routers.http-catchall.entrypoints=web"
        - "traefik.http.routers.http-catchall.middlewares=redirect-https@docker"
        - "traefik.http.routers.http-catchall.priority=1"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "traefik_certificates_volume:/etc/traefik/letsencrypt"
    ports:
      - target: 80
        published: 80
        mode: host
      - target: 443
        published: 443
        mode: host
    networks:
      - ecosystem_network

volumes:
  traefik_certificates_volume:
    external: true
    name: traefik_certificates_volume

networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
 ```

```bash
nano portainer.yml
```

```bash
version: "3.8"

services:

  agent:
    image: portainer/agent:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
    networks:
      - ecosystem_network
    deploy:
      mode: global
      placement:
        constraints: [node.platform.os == linux]

  portainer:
    image: portainer/portainer-ce:latest
    command: -H tcp://tasks.agent:9001 --tlsskipverify
    volumes:
      - portainer_volume:/data
    networks:
      - ecosystem_network
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]
      labels:
        - "traefik.enable=true"
        - "traefik.docker.network=ecosystem_network"
        - "traefik.http.routers.portainer.rule=Host(`seudominio.com.br`)"
        - "traefik.http.routers.portainer.entrypoints=websecure"
        - "traefik.http.routers.portainer.priority=1"
        - "traefik.http.routers.portainer.tls.certresolver=letsencryptresolver"
        - "traefik.http.routers.portainer.service=portainer"
        - "traefik.http.services.portainer.loadbalancer.server.port=9000"

networks:
  ecosystem_network:
    external: true
    attachable: true
    name: ecosystem_network

volumes:
  portainer_volume:
    external: true
    name: portainer_volume

 ```

```bash
```

docker swarm init
```bash
docker network create --driver=overlay ecosystem_network
```

```bash
docker stack deploy --prune --resolve-image always -c traefik.yml traefik
```

```bash
docker stack deploy --prune --resolve-image always -c portainer.yml portainer
```

Acesse URL de seu Site e Crie Usuario


</details>

Postgres

### Adicione Stack abaixo, Stack > add stack

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/623a6dc6-c231-4105-9a02-3070d894adb8)

```bash
version: "3.8"

services:

  postgresql:
    image: bitnami/postgresql:15
    restart: always
    environment:
      POSTGRESQL_USERNAME: postgres
      POSTGRESQL_PASSWORD: r45796yv3bhub9w4f3ga3ikxmxos648r
    networks:
      - ecosystem_network
    ports:
      - 5432:5432
    volumes:
      - postgresql_volume:/bitnami/postgresql
      - postgresql_config_volume:/opt/bitnami/postgresql/conf
    deploy:
    

volumes:
  postgresql_volume:
    external: true
    name: postgresql_volume
  postgresql_config_volume:
    external: true
    name: postgresql_config_volume
    
networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
```

Depois clique em DEPLOY

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/bdc62781-993a-4d31-b8cd-5cd6466900f5)

Acesse portainer, vá até contaneir Postgres, crie banco de dados

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/503bf33f-ff42-4fe5-9a8f-a98e2d80d6e4)

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/67eb98c2-f7e7-4f27-ae9d-1befc672edcf)


```bash
psql -U postgres
```

```bash
create database evolution;
```

Redis
Para instalar o redis vamos fazer as mesmas etapas que fizemos no postgres: Vamos para aba Stacks e depois em add Stack

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/07c67cb5-465c-4e05-88e7-164ec8456f00)

Colocamos o nome da stack (recomendo deixar redis)

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/03d15854-59bc-47df-a983-ad9adf0c93c4)

Cole a stack do redis abaixo no seu portainer:

```bash
version: '3.7'

services:
  redis:
    image: redis:latest
    command: [
        "redis-server",
        "--appendonly",
        "yes",
        "--port",
        "6379"
      ]
    volumes:
      - redis_data:/data
    networks:
      - ecosystem_network
    deploy:
      placement:
        constraints:
          - node.role == manager

volumes:
  redis_data:
    external: true
    name: redis_woofedcrm_data

networks:
  ecosystem_network
    external: true
    ecosystem_network
```

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/dedb5386-bc42-465c-a39e-1ff2aa131f85)

e pronto, o redis esta instalado e funcionando na sua VPS



Para instalar o Evolution, Vamos para aba Stacks e depois em add Stack

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/d1e54ab7-5c5f-4c28-902f-27266ed0abb7)

Colocamos o nome da stack (recomendo deixar evolution)


![image](https://github.com/cwmkt/dockerevolution/assets/91642837/ec07266c-d63b-48bc-a658-ccedb4fc3662)


#### Cole a stack do Evolution abaixo no seu portainer:

```bash
version: "3.7"

services:
  evolution_api_v2:
    volumes:
      - evolution_instances:/evolution/instances
    networks:
      - ecosystem_network
    environment:
      # Configurar el servidor de evolution
      - SERVER_TYPE=http
      - SERVER_PORT=8080
      - SERVER_URL=https://evolution.seudominio.com.br
      # Configurar CORS
      - CORS_ORIGIN=*
      - CORS_METHODS=POST,GET,PUT,DELETE
      - CORS_CREDENTIALS=true
      # Configurar autenticación
      - AUTHENTICATION_API_KEY=429683C4C977415CAAFCCE10F7D57E11
      - AUTHENTICATION_EXPOSE_IN_FETCH_INSTANCES=true
      # Determina los logs que serán mostrados
      - LOG_LEVEL=ERROR,WARN
      - LOG_COLOR=true
      - LOG_BAILEYS=error
      # Información que se mostrará en los smartphones
      - CONFIG_SESSION_PHONE_CLIENT=Evolution API V2
      - CONFIG_SESSION_PHONE_NAME=Chrome
      - CONFIG_SESSION_PHONE_VERSION=2.2413.51
      # Configurar el QR code
      - QRCODE_LIMIT=30
      - QRCODE_COLOR=#000000
      # Determina si se elimarán las instancias no conectadas
      - DEL_INSTANCE=1902
      - DEL_TEMP_INSTANCES=false
      # Configurar provider (alternativa a base de datos)
      - PROVIDER_ENABLED=false
      - PROVIDER_HOST=127.0.0.1
      - PROVIDER_PORT=5656
      - PROVIDER_PREFIX=evolution_api
      # Configurar base de datos (Postgres)
      - DATABASE_ENABLED=true
      - DATABASE_PROVIDER=postgresql
      - DATABASE_CONNECTION_URI=postgresql://postgres:r45796yv3bhub9w4f3ga3ikxmxos648r@postgres:5432/evolution?schema=public
      - DATABASE_CONNECTION_CLIENT_NAME=evolution_api
      - DATABASE_SAVE_DATA_INSTANCE=true
      - DATABASE_SAVE_DATA_NEW_MESSAGE=true
      - DATABASE_SAVE_MESSAGE_UPDATE=true
      - DATABASE_SAVE_DATA_CONTACTS=true
      - DATABASE_SAVE_DATA_CHATS=true
      # Configurar RabbitMQ
      - RABBITMQ_ENABLED=false
      - RABBITMQ_URI=amqp://usuario:PASSWORD@rabbitmq:5672/default
      - RABBITMQ_EXCHANGE_NAME=evolution_api
      - RABBITMQ_GLOBAL_ENABLED=true
      - RABBITMQ_EVENTS_APPLICATION_STARTUP=true
      - RABBITMQ_EVENTS_INSTANCE_CREATE=true
      - RABBITMQ_EVENTS_INSTANCE_DELETE=true
      - RABBITMQ_EVENTS_QRCODE_UPDATED=true
      - RABBITMQ_EVENTS_MESSAGES_SET=true
      - RABBITMQ_EVENTS_MESSAGES_UPSERT=true
      - RABBITMQ_EVENTS_MESSAGES_EDITED=true
      - RABBITMQ_EVENTS_MESSAGES_UPDATE=true
      - RABBITMQ_EVENTS_MESSAGES_DELETE=true
      - RABBITMQ_EVENTS_SEND_MESSAGE=true
      - RABBITMQ_EVENTS_CONTACTS_SET=true
      - RABBITMQ_EVENTS_CONTACTS_UPSERT=true
      - RABBITMQ_EVENTS_CONTACTS_UPDATE=true
      - RABBITMQ_EVENTS_PRESENCE_UPDATE=true
      - RABBITMQ_EVENTS_CHATS_SET=true
      - RABBITMQ_EVENTS_CHATS_UPSERT=true
      - RABBITMQ_EVENTS_CHATS_UPDATE=true
      - RABBITMQ_EVENTS_CHATS_DELETE=true
      - RABBITMQ_EVENTS_GROUPS_UPSERT=true
      - RABBITMQ_EVENTS_GROUP_UPDATE=true
      - RABBITMQ_EVENTS_GROUP_PARTICIPANTS_UPDATE=true
      - RABBITMQ_EVENTS_CONNECTION_UPDATE=true
      - RABBITMQ_EVENTS_CALL=true
      - RABBITMQ_EVENTS_TYPEBOT_START=true
      - RABBITMQ_EVENTS_TYPEBOT_CHANGE_STATUS=true
      # Configurar SQS
      - SQS_ENABLED=false
      - SQS_ACCESS_KEY_ID=
      - SQS_SECRET_ACCESS_KEY=
      - SQS_ACCOUNT_ID=
      - SQS_REGION=
      # Configurar websocket:
      - WEBSOCKET_ENABLED=false
      - WEBSOCKET_GLOBAL_EVENTS=false
      # Configurar API oficial de WhatsApp
      - WA_BUSINESS_TOKEN_WEBHOOK=evolution
      - WA_BUSINESS_URL=https://graph.facebook.com
      - WA_BUSINESS_VERSION=v18.0
      - WA_BUSINESS_LANGUAGE=es
      # Configurar el webhook global
      - WEBHOOK_GLOBAL_ENABLED=false
      - WEBHOOK_GLOBAL_URL=https://URL
      - WEBHOOK_GLOBAL_WEBHOOK_BY_EVENTS=false
      - WEBHOOK_EVENTS_APPLICATION_STARTUP=false
      - WEBHOOK_EVENTS_QRCODE_UPDATED=true
      - WEBHOOK_EVENTS_MESSAGES_SET=true
      - WEBHOOK_EVENTS_MESSAGES_UPSERT=true
      - WEBHOOK_EVENTS_MESSAGES_EDITED=true
      - WEBHOOK_EVENTS_MESSAGES_UPDATE=true
      - WEBHOOK_EVENTS_MESSAGES_DELETE=true
      - WEBHOOK_EVENTS_SEND_MESSAGE=true
      - WEBHOOK_EVENTS_CONTACTS_SET=true
      - WEBHOOK_EVENTS_CONTACTS_UPSERT=true
      - WEBHOOK_EVENTS_CONTACTS_UPDATE=true
      - WEBHOOK_EVENTS_PRESENCE_UPDATE=true
      - WEBHOOK_EVENTS_CHATS_SET=true
      - WEBHOOK_EVENTS_CHATS_UPSERT=true
      - WEBHOOK_EVENTS_CHATS_UPDATE=true
      - WEBHOOK_EVENTS_CHATS_DELETE=true
      - WEBHOOK_EVENTS_GROUPS_UPSERT=true
      - WEBHOOK_EVENTS_GROUPS_UPDATE=true
      - WEBHOOK_EVENTS_GROUP_PARTICIPANTS_UPDATE=true
      - WEBHOOK_EVENTS_CONNECTION_UPDATE=true
      - WEBHOOK_EVENTS_LABELS_EDIT=true
      - WEBHOOK_EVENTS_LABELS_ASSOCIATION=true
      - WEBHOOK_EVENTS_CALL=true
      - WEBHOOK_EVENTS_TYPEBOT_START=false
      - WEBHOOK_EVENTS_TYPEBOT_CHANGE_STATUS=false
      - WEBHOOK_EVENTS_ERRORS=false
      - WEBHOOK_EVENTS_ERRORS_WEBHOOK=https://evolution.seudominio.com.br/webhook
      # Configurar Typebot
      - TYPEBOT_ENABLED=true
      - TYPEBOT_SEND_MEDIA_BASE64=true
      - TYPEBOT_API_VERSION=latest
      # Configurar Chatwoot
      - CHATWOOT_ENABLED=true
      - CHATWOOT_MESSAGE_READ=true
      - CHATWOOT_MESSAGE_DELETE=true
      - CHATWOOT_IMPORT_DATABASE_CONNECTION_URI=postgresql://postgres:1r45796yv3bhub9w4f3ga3ikxmxos648r@postgres:5432/chatwoot?sslmode=disable
      - CHATWOOT_IMPORT_PLACEHOLDER_MEDIA_MESSAGE=true
      - LANGUAGE=pt_BR
      # Redis
      - CACHE_REDIS_ENABLED=true
      - CACHE_REDIS_URI=redis://redis:6379/6
      - CACHE_REDIS_PREFIX_KEY=evolution_api
      - CACHE_REDIS_SAVE_INSTANCES=false
      - CACHE_LOCAL_ENABLED=false
	  # S3 Storage
	  - S3_ENABLED=false
      - S3_ACCESS_KEY=
      - S3_SECRET_KEY=
      - S3_BUCKET=evolution
      - S3_PORT=443
      - S3_ENDPOINT=files.site.com
      - S3_USE_SSL=true
      - AUTHENTICATION_API_KEY=429683C4C977415CAAFCCE10F7D57E11
      - AUTHENTICATION_EXPOSE_IN_FETCH_INSTANCES=true
      - LANGUAGE=en
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.role == manager
      resources:
        limits:
          cpus: "1"
          memory: 1024M
      labels:
        traefik.enable: "true"
        traefik.http.routers.evolution_api_v2.rule: "Host(`evolution.seudominio.com.br`)"
        traefik.http.routers.evolution_api_v2.entrypoints: "websecure"
        traefik.http.routers.evolution_api_v2.tls.certresolver: "letsencryptresolver"
        traefik.http.routers.evolution_api_v2.priority: "1"
        traefik.http.routers.evolution_api_v2.service: "evolution_api_v2"
        traefik.http.services.evolution_api_v2.loadbalancer.server.port: "8080"
        traefik.http.services.evolution_api_v2.loadbalancer.passHostHeader: "true"

volumes:
  evolution_instances:
    external: true
    name: evolution_v2_data

networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
```

### Obs: Lembre-se de alterar campos com as informações necessárias:

SERVER_URL: URL para API <br>
CONFIG_SESSION_PHONE_CLIENT: Nome da sessão no APP do Whatsapp <br>
traefik.http.routers.evolution_app.rule=Host(`api.seudominio.com.br`) <br>

Após toda alteração, podemos desativar o Enable access control e clicar em Deploy the stack

![image](https://github.com/cwmkt/woofedcrm/assets/91642837/e39d7915-e223-4afe-98d0-fdc369138265)

**Pronto tudo Funcionando** ✅😎
