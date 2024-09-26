# Guide to Setting Up Basic whatsapp-web.js app with Docker

## Prerequisites

- Basic knowledge of Docker
- [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/install/) installed

## Step 1: Configure your `Dockerfile` to use `whatsapp-web.js`

```dockerfile
FROM node:slim

# Install ffmpeg and chromium dependencies
RUN apt-get update && apt-get install -y \
    ffmpeg \ 
    nano \
    zip unzip \
    fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst fonts-freefont-ttf \
    chromium \
    --no-install-recommends && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install app dependencies
COPY package*.json ./
RUN npm install --only=production \
    && npm cache clean --force

COPY . .
# RUN cp /app/.env.example /app/.env

# Expose port if you have any API server inside your project
EXPOSE 3000

CMD ["npm", "start"]
```

## Step 2: Setup your `docker-compose.yaml` file

This is the `docker-compose.yaml` configuration with the `app` and `redis` containers.

```yaml
services:
  app:
    build: .
    image: <mydockerusername>/<mysuperbotname>:latest>
    container_name: <mysuperbotname>
    restart: unless-stopped
    ports:
      - 3000:3000
    networks:
      - <mybotnetwork>
    depends_on:
      - redis
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379

  redis:
    image: redis:alpine
    container_name: redis
    restart: unless-stopped
    networks:
      - <mybotnetwork>
    volumes:
      - redis-data:/data

networks:
  mybotnetwork:
    driver: bridge

volumes:
  redis-data:
```

## Step 3: Build and Start the Docker Containers

Navigate to your project directory, build and run the containers using Docker Compose: 

```sh
docker build . --no-cache ; docker-compose up
```
