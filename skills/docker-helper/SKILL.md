---
name: docker-helper
description: |
  Docker and container management via PowerShell scripts for local Windows machine. Write Dockerfiles, docker-compose.yml files, manage containers (start/stop/logs/exec). Common setups: databases (Postgres, MySQL, Redis, MongoDB), web servers (nginx, Apache), dev stacks (MERN, LAMP, Django). Troubleshoot container issues, manage volumes and networks, prune unused resources.
---

# Docker Helper â Container Management for Windows

Build, run, and manage Docker containers on your local Windows machine. Generate Dockerfiles, docker-compose configurations, and PowerShell management scripts for databases, web servers, and complete development stacks.

## Docker Installation (Windows)

### Install Docker Desktop

```powershell
# Install via Chocolatey
choco install docker-desktop -y

# Restart and start Docker Desktop
Restart-Computer

# Verify installation
docker --version
docker run hello-world
```

### Enable WSL2 Backend (recommended)

```powershell
# Enable WSL2 feature
wsl --install -d Ubuntu-22.04

# In Docker Desktop Settings:
# - Settings > Resources > WSL integration
# - Enable "Ubuntu-22.04"
```

## Basic Docker Commands

### Container Lifecycle

```powershell
# Run a container
docker run -d --name myapp -p 8080:80 nginx:latest

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# View container logs
docker logs myapp
docker logs -f myapp  # Follow logs

# Stop container
docker stop myapp

# Start stopped container
docker start myapp

# Remove container
docker rm myapp  # Must stop first
docker rm -f myapp  # Force remove

# Execute command in running container
docker exec -it myapp /bin/bash

# View container resource usage
docker stats

# Inspect container details
docker inspect myapp
```

### Image Management

```powershell
# List images
docker images

# Pull image from registry
docker pull python:3.11

# Build image from Dockerfile
docker build -t myapp:1.0 .

# Tag image
docker tag myapp:1.0 myapp:latest

# Push image to registry
docker push myregistry.azurecr.io/myapp:1.0

# Remove image
docker rmi myapp:1.0

# Remove unused images
docker image prune -a -f
```

## Common Dockerfiles

### Python Web App (Flask/FastAPI)

**Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Copy requirements first (for better caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**requirements.txt:**
```
fastapi==0.104.1
uvicorn==0.24.0
sqlalchemy==2.0.23
python-dotenv==1.0.0
```

### Node.js Web App (Express)

**Dockerfile:**
```dockerfile
FROM node:20-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./
RUN npm ci --only=production

# Copy source
COPY . .

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD node -e "require('http').get('http://localhost:3000/health', (r) => {if (r.statusCode !== 200) throw new Error(r.statusCode)})"

CMD ["node", "index.js"]
```

### Java Application

**Dockerfile:**
```dockerfile
FROM openjdk:21-jdk-slim

WORKDIR /app

# Copy JAR
COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### .NET Application

**Dockerfile:**
```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 as builder
WORKDIR /src
COPY . .
RUN dotnet publish -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/runtime:8.0
WORKDIR /app
COPY --from=builder /app/publish .
EXPOSE 80
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

## Docker Compose for Multi-Container Stacks

### PostgreSQL + Python Backend + Web Server

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  database:
    image: postgres:16-alpine
    container_name: pg_database
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: password123
      POSTGRES_DB: myapp_db
    ports:
      - "5432:5432"
    volumes:
      - pg_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      timeout: 5s
      retries: 5

  backend:
    build: ./backend
    container_name: app_backend
    environment:
      DATABASE_URL: postgresql://admin:password123@database:5432/myapp_db
      DEBUG: "false"
    ports:
      - "8000:8000"
    depends_on:
      database:
        condition: service_healthy
    volumes:
      - ./backend:/app
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload

  nginx:
    image: nginx:alpine
    container_name: app_nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./certs:/etc/nginx/certs:ro
    depends_on:
      - backend

volumes:
  pg_data:

networks:
  default:
    name: myapp_network
```

### MySQL + Node.js Backend + React Frontend

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  database:
    image: mysql:8.0
    container_name: mysql_db
    environment:
      MYSQL_ROOT_PASSWORD: root_pass
      MYSQL_USER: appuser
      MYSQL_PASSWORD: app_pass
      MYSQL_DATABASE: myapp_db
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./schema.sql:/docker-entrypoint-initdb.d/schema.sql

  backend:
    build: ./api
    container_name: node_backend
    environment:
      DB_HOST: database
      DB_USER: appuser
      DB_PASSWORD: app_pass
      DB_NAME: myapp_db
      NODE_ENV: development
    ports:
      - "5000:5000"
    depends_on:
      - database
    volumes:
      - ./api:/app
      - /app/node_modules

  frontend:
    build: ./client
    container_name: react_frontend
    ports:
      - "3000:3000"
    environment:
      REACT_APP_API_URL: http://localhost:5000
    volumes:
      - ./client:/app
      - /app/node_modules

volumes:
  mysql_data:
```

### MERN Stack (MongoDB, Express, React, Node)

```yaml
version: '3.8'
services:
  mongodb:
    image: mongo:7.0
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password123
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  api:
    build: ./server
    ports:
      - "5000:5000"
    environment:
      MONGODB_URI: mongodb://admin:password123@mongodb:27017/mern_db?authSource=admin
    depends_on:
      - mongodb

  client:
    build: ./client
    ports:
      - "3000:3000"
    environment:
      REACT_APP_API_URL: http://localhost:5000/api

volumes:
  mongo_data:
```

## Database Containers

### PostgreSQL

```yaml
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./backup.sql:/docker-entrypoint-initdb.d/backup.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s

volumes:
  postgres_data:
```

### MySQL / MongoDB / Redis

```yaml
# MySQL
services:
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root_pass
      MYSQL_DATABASE: myapp
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

# MongoDB
services:
  mongodb:
    image: mongo:7.0
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: secret
    ports:
      - "27017:27017"

# Redis
services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --appendonly yes
```

## Docker Management PowerShell Scripts

### Quick Stack Management

```powershell
# Up
docker-compose up -d

# Down
docker-compose down

# Logs (follow)
docker-compose logs -f

# Status
docker-compose ps

# Restart
docker-compose restart

# Clean
docker-compose down -v && docker system prune -f
```

### Container Quick Commands

```powershell
# List containers with ports
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Logs
docker logs -f container_name

# Execute command
docker exec -it container_name /bin/bash

# Stop/remove
docker stop container_name
docker rm container_name
```

### Build and Push

```powershell
param([string]$Registry, [string]$Image, [string]$Version)
docker build -t "$Registry/$Image:$Version" .
docker push "$Registry/$Image:$Version"
```

## Troubleshooting

```powershell
# Check daemon
docker info

# View all containers
docker ps -a

# Container logs
docker logs --tail 100 container_name

# Container stats
docker stats

# Inspect config
docker inspect container_name

# Copy files from container
docker cp container_name:/path ./local/

# Networks
docker network ls
docker network inspect myapp_net

# Volumes
docker volume ls
docker volume inspect volume_name

# Cleanup unused
docker system prune -a -f
```

## Quick Reference

| Task | Command |
|------|---------|
| Run container | `docker run -d --name app -p 8080:80 image:tag` |
| View logs | `docker logs -f container_name` |
| Stop container | `docker stop container_name` |
| Remove container | `docker rm container_name` |
| Build image | `docker build -t image:tag .` |
| Execute in container | `docker exec -it container /bin/bash` |
| Compose up | `docker-compose up -d` |
| Compose down | `docker-compose down` |
| List containers | `docker ps -a` |
| List images | `docker images` |
| List volumes | `docker volume ls` |
| View stats | `docker stats` |
