
## 🐳 **Docker Compose – Overview **

### 🔹 What is Docker Compose?

Docker Compose is a **tool** used to define and run **multi-container Docker applications** using a simple YAML file (`docker-compose.yml`).

### 🔹 Why use Docker Compose?

* Easily manage multiple containers (e.g., app + db + cache)
* Start/stop the entire app with one command
* Good for **local development**, testing, and simple deployment

### 🔹 Compose File Example:

```yaml
version: "3"
services:
  web:
    image: nginx
    ports:
      - "80:80"
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: password
```

### 🔹 Key Commands:

```bash
docker-compose up         # Start all services
docker-compose down       # Stop and remove services
docker-compose ps         # List running services
docker-compose logs       # Show logs
```

---

## 🐳 Docker Swarm – Overview for Students

### 🔹 What is Docker Swarm?

Docker Swarm is Docker’s **native clustering and orchestration tool** that lets you manage **a cluster of Docker nodes** as a single virtual system.

### 🔹 Why use Docker Swarm?

* High availability
* Load balancing
* Scaling containers
* Easy to set up compared to Kubernetes

### 🔹 Swarm Architecture:

* **Manager Node**: Controls the cluster, makes decisions
* **Worker Nodes**: Run services/tasks

---

## 🚀 Docker Swarm Hands-on

### ✅ Initialize Swarm:

```bash
docker swarm init
```

### ✅ Deploy a service:

```bash
docker service create --name web --replicas 3 -p 80:80 nginx
```

### ✅ Check running services:

```bash
docker service ls
docker service ps web
```

### ✅ Scale the service:

```bash
docker service scale web=5
```

### ✅ Remove service:

```bash
docker service rm web
```

---

## 📌 Docker Compose vs Docker Swarm

| Feature        | Docker Compose               | Docker Swarm                |
| -------------- | ---------------------------- | --------------------------- |
| Use case       | Local multi-container setups | Production-level clustering |
| Orchestration  | No                           | Yes (built-in)              |
| Scaling        | Manual                       | Automatic via `--replicas`  |
| Load Balancing | No                           | Yes                         |
