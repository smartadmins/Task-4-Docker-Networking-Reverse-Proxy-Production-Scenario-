# 🐳 Docker Networking & Reverse Proxy with Nginx

> **Enterprise DevOps Scenario:** Route multiple backend microservices through a single Nginx reverse proxy using Docker Networking and Docker Compose.

![Docker](https://img.shields.io/badge/Docker-Networking-blue)
![Nginx](https://img.shields.io/badge/Nginx-Reverse%20Proxy-green)
![Node.js](https://img.shields.io/badge/Node.js-Backend-brightgreen)
![Docker Compose](https://img.shields.io/badge/Docker%20Compose-Orchestration-orange)
![License](https://img.shields.io/badge/License-MIT-blue)

---

# 📖 Project Overview

In a production microservices architecture, exposing every container directly to users is neither secure nor scalable.

Instead, a **Reverse Proxy** acts as a single entry point for all incoming requests and routes traffic to the appropriate backend services.

This project demonstrates how to:

* Deploy multiple backend services
* Connect containers using a custom Docker network
* Route requests using Nginx
* Manage the environment with Docker Compose
* Implement production-ready reverse proxy architecture

---

# 🎯 Objectives

* Build multiple containerized backend services
* Configure Docker Networking
* Deploy an Nginx Reverse Proxy
* Route traffic using URL paths
* Orchestrate services with Docker Compose
* Prepare the architecture for horizontal scaling

---

# 🧩 Production Scenario

Your organization has migrated from a monolithic application to a microservices architecture.

Instead of exposing every service individually, all incoming traffic must pass through an **Nginx Reverse Proxy**.

The proxy is responsible for:

* Routing requests
* Hiding internal container addresses
* Simplifying client access
* Preparing for future load balancing
* Improving security

---

# 🏗 Architecture

```text
                    Client Browser
                           │
                    http://localhost
                           │
                           ▼
                +----------------------+
                |   Nginx Reverse Proxy|
                +----------------------+
                   │              │
          /users   │              │  /products
                   ▼              ▼
         +----------------+   +-------------------+
         | User Service   |   | Product Service   |
         | Node.js        |   | Node.js           |
         +----------------+   +-------------------+
                   │              │
                   └──────┬───────┘
                          │
                 Docker Custom Network
```

---

# 📂 Project Structure

```text
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
```

---

# ⚙️ Technologies Used

| Technology        | Purpose                       |
| ----------------- | ----------------------------- |
| Docker            | Containerization              |
| Docker Compose    | Multi-container orchestration |
| Docker Networking | Service communication         |
| Node.js           | Backend services              |
| Express           | Web framework                 |
| Nginx             | Reverse Proxy                 |
| Alpine Linux      | Lightweight container images  |

---

# 🚀 Application Components

## User Service

Returns:

```text
User Service Running
```

---

## Product Service

Returns:

```text
Product Service Running
```

---

## Nginx Reverse Proxy

Routes incoming requests:

| URL       | Destination     |
| --------- | --------------- |
| /users    | User Service    |
| /products | Product Service |

---

# 🌐 Docker Networking

This project creates a **custom bridge network** allowing containers to communicate securely using Docker DNS.

```
Docker Network
      │
      ├── nginx
      ├── user-service
      └── product-service
```

Containers communicate internally using service names instead of IP addresses.

Example:

```
http://user-service:3000
```

instead of

```
http://172.x.x.x:3000
```

---

# ⚙️ Reverse Proxy Flow

```
Browser
    │
GET /users
    │
    ▼
Nginx
    │
Proxy Pass
    │
    ▼
User Service
```

Likewise,

```
Browser
    │
GET /products
    │
    ▼
Nginx
    │
Proxy Pass
    │
    ▼
Product Service
```

---

# 🚀 Getting Started

## Clone Repository

```bash
git clone https://github.com/smartadmins/docker-networking-reverse-proxy.git

cd docker-networking-reverse-proxy
```

---

## Build Images

```bash
docker compose build
```

---

## Start Services

```bash
docker compose up -d
```

---

## Verify Running Containers

```bash
docker ps
```

Expected:

```
reverse-proxy

user-service

product-service
```

---

# 🌍 Testing

### User Service

```
http://localhost/users
```

Response

```
User Service Running
```

---

### Product Service

```
http://localhost/products
```

Response

```
Product Service Running
```

---

# 🔍 Verify Docker Network

List networks

```bash
docker network ls
```

Inspect network

```bash
docker network inspect 4-docker-networking-reverse-proxy-production-scenario_backend
```

Expected containers

```
reverse-proxy

user-service

product-service
```

---

# 📦 Docker Compose Services

| Service         | Port | Purpose       |
| --------------- | ---- | ------------- |
| nginx           | 80   | Reverse Proxy |
| user-service    | 3000 | User API      |
| product-service | 3000 | Product API   |

---

# 🔀 Request Routing

```
Client Request
       │
       ▼
Nginx
   │
   ├──────────────► /users
   │                    │
   │                    ▼
   │             User Service
   │
   └──────────────► /products
                        │
                        ▼
                 Product Service
```

---

# 💡 Bonus Features

## ✅ Load Balancing

Scale the User Service

```bash
docker compose up -d --scale user-service=3
```

Configure Nginx using **upstream** blocks for distributing traffic across multiple instances.

---

## ✅ Custom Docker Network

Create a dedicated bridge network for secure inter-container communication.

---

## ✅ Health Checks

Monitor container health automatically.

Example:

```yaml
healthcheck:
  test: ["CMD","wget","--spider","http://localhost:3000"]
  interval: 30s
  timeout: 5s
  retries: 3
```

---

## ✅ Security Best Practices

* Run containers as non-root users
* Use lightweight Alpine images
* Minimize Docker image size
* Keep internal services inaccessible from outside
* Expose only the reverse proxy

---

## ✅ Production Improvements

* HTTPS with SSL certificates
* Rate limiting
* Authentication
* Caching
* Compression (Gzip)
* Monitoring with Prometheus & Grafana
* Centralized logging (ELK/Loki)
* CI/CD using GitHub Actions or Jenkins

---

# 📚 DevOps Concepts Demonstrated

* Docker Networking
* Docker Compose
* Multi-container Applications
* Reverse Proxy
* Nginx Configuration
* Service Discovery
* Microservices Architecture
* Path-based Routing
* Container Isolation
* Production Networking
* Horizontal Scaling
* Load Balancing Fundamentals

---

# 📸 Screenshots

Add screenshots to the `screenshots/` directory.

Suggested images:

```
screenshots/
│
├── docker-ps.png
├── docker-network.png
├── users-page.png
├── products-page.png
└── nginx-container.png
```

---

# 🏆 Learning Outcomes

By completing this project, you will understand how to:

* Build multiple Docker images
* Connect containers through Docker networking
* Configure an Nginx reverse proxy
* Route traffic using URL paths
* Orchestrate services with Docker Compose
* Prepare applications for production deployment
* Implement scalable microservice networking

---

# 🤝 Contributing

Contributions are welcome!

1. Fork the repository
2. Create a feature branch

```bash
git checkout -b feature-name
```

3. Commit your changes

```bash
git commit -m "Added new feature"
```

4. Push to GitHub

```bash
git push origin feature-name
```

5. Open a Pull Request

---

# 📄 License

This project is licensed under the **MIT License**.

---

# 👨‍💻 Author

**Sudheesh K**

**Senior Infrastructure Lead | DevOps & Cloud Engineer**

**Skills**

* Docker
* Kubernetes
* AWS
* Terraform
* Jenkins
* GitHub Actions
* Linux
* Nginx
* Ansible
* Prometheus
* Grafana

---

⭐ **If you found this project helpful, don't forget to star the repository!**
