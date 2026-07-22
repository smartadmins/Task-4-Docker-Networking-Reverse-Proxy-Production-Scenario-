# Task-4-Docker-Networking-Reverse-Proxy-Production-Scenario-
This is an excellent production-style DevOps project that demonstrates Docker networking, service discovery, reverse proxying, and load balancing. It also makes a strong GitHub portfolio project for DevOps and Cloud Engineer roles.

Project Structure
docker-networking-reverse-proxy/
│
├── docker-compose.yml
├── README.md
│
├── nginx/
│   ├── Dockerfile
│   └── nginx.conf
│
├── user-service/
│   ├── Dockerfile
│   ├── package.json
│   └── server.js
│
├── product-service/
│   ├── Dockerfile
│   ├── package.json
│   └── server.js
│
├── architecture/
│   ├── architecture.md
│   └── architecture.png
│
└── screenshots/
    ├── users.png
    ├── products.png
    └── docker-ps.png
Architecture
                    Browser
                       │
                       ▼
               localhost:80
                       │
              ┌─────────────────┐
              │   Nginx Proxy   │
              └─────────────────┘
                 │           │
                 │           │
      /users     │           │     /products
                 ▼           ▼
      ┌────────────────┐  ┌─────────────────┐
      │ User Service   │  │ Product Service │
      │ NodeJS         │  │ NodeJS          │
      └────────────────┘  └─────────────────┘
                 │
                 ▼
          Docker Bridge Network
Step 1 User Service
server.js
const express = require('express');

const app = express();

app.get('/', (req,res)=>{
    res.send("User Service Running");
});

app.listen(3000,()=>{
    console.log("User Service Started");
});
package.json
{
  "name": "user-service",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.19.2"
  },
  "scripts": {
    "start": "node server.js"
  }
}
Dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY . .

EXPOSE 3000

CMD ["npm","start"]
Step 2 Product Service

server.js

const express = require('express');

const app = express();

app.get('/', (req,res)=>{
    res.send("Product Service Running");
});

app.listen(3000,()=>{
    console.log("Product Service Started");
});

package.json

{
  "name": "product-service",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.19.2"
  },
  "scripts": {
    "start": "node server.js"
  }
}

Dockerfile

FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY . .

EXPOSE 3000

CMD ["npm","start"]
Step 3 Nginx Configuration
nginx.conf
events {}

http {

    upstream users_backend {

        server user-service:3000;

    }

    upstream products_backend {

        server product-service:3000;

    }

    server {

        listen 80;

        location /users {

            proxy_pass http://users_backend/;

            proxy_set_header Host $host;

            proxy_set_header X-Real-IP $remote_addr;

        }

        location /products {

            proxy_pass http://products_backend/;

            proxy_set_header Host $host;

            proxy_set_header X-Real-IP $remote_addr;

        }

    }

}
Step 4 Nginx Dockerfile
FROM nginx:alpine

COPY nginx.conf /etc/nginx/nginx.conf
Step 5 Docker Compose
version: "3.9"

services:

  user-service:
    build:
      context: ./user-service
    container_name: user-service
    networks:
      - backend

  product-service:
    build:
      context: ./product-service
    container_name: product-service
    networks:
      - backend

  nginx:
    build:
      context: ./nginx
    container_name: reverse-proxy
    ports:
      - "80:80"
    depends_on:
      - user-service
      - product-service
    networks:
      - backend

networks:

  backend:
    driver: bridge
Build
docker compose build
Run
docker compose up -d
Verify
docker ps

Visit

http://localhost/users

Output

User Service Running

Visit

http://localhost/products

Output

Product Service Running
Verify Network
docker network ls

Inspect

docker network inspect docker-networking-reverse-proxy_backend

You should see

user-service

product-service

reverse-proxy
Bonus 1 – Load Balancing

Scale User Service

docker compose up -d --scale user-service=3

Nginx

upstream users_backend {

    server user-service:3000;

    server user-service:3000;

    server user-service:3000;

}

A more production-friendly approach is to use multiple uniquely named service instances or Docker Swarm/Kubernetes, where Nginx can balance across distinct endpoints.

Bonus 2 – Custom Docker Network
networks:

  backend:

    driver: bridge

    ipam:

      config:

      - subnet: 172.30.0.0/16
Bonus 3 – Health Check
healthcheck:
  test: ["CMD","wget","--spider","http://localhost:3000"]
  interval: 30s
  timeout: 5s
  retries: 3
Bonus 4 – Security

Run Node.js as a non-root user.

FROM node:22-alpine

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY . .

USER appuser

EXPOSE 3000

CMD ["npm","start"]
Bonus 5 – Production Nginx
server_tokens off;

gzip on;

client_max_body_size 10M;

proxy_read_timeout 60;
Project Workflow
Client Browser
      │
      ▼
localhost:80
      │
      ▼
Nginx Reverse Proxy
      │
 ┌────┴────┐
 │         │
 ▼         ▼
/users   /products
 │         │
 ▼         ▼
User     Product
Service  Service
 │         │
 └────┬────┘
      ▼
Docker Bridge Network
DevOps concepts demonstrated
Docker image creation for multiple microservices
Custom Docker bridge networking
Docker Compose orchestration
Nginx reverse proxy configuration
Path-based routing (/users, /products)
Service discovery using Docker DNS
Health checks
Running containers as non-root users
Production-ready Nginx hardening
Foundation for horizontal scaling and load balancing
