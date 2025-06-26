---
title: "Docker Container Guide"
date: 2025-06-24
tags: ["docker", "containers", "devops"]
categories: ["devops"]
weight: 1
---

# Docker Container Guide

Learn how to use Docker for application deployment.

## What is Docker?

Docker is a platform for developing, shipping, and running applications in containers.

## Basic Commands

```bash
# Pull an image
docker pull nginx

# Run a container
docker run -d -p 80:80 nginx

# List running containers
docker ps

# Stop a container
docker stop container_id

# Remove a container
docker rm container_id
```

## Creating a Dockerfile

```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

## Docker Compose

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
  
  db:
    image: postgres:13
    environment:
      - POSTGRES_PASSWORD=secret
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```
