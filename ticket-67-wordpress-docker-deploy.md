
| **Field** | Value |
| --- | --- |
| **Ticket #** | 67 |
| **Title** | Deploy WordPress via Docker Containers** *Docker · MariaDB · WordPress · Container Networking · Port Mapping* |
| **Category** | Web Server Administration |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |






## 1. Objective

The Public Relations department wants to deploy a WordPress website. This task installs Docker on dev-app, then runs a MariaDB database container and a WordPress container, linked together and accessible via port 8080.

> **  Task Requirements** VM: dev-app Install and configure Docker CE on dev-app Deploy MariaDB container as the WordPress database backend Deploy WordPress container linked to MariaDB on port 8080 Provide IP address and URL of working WordPress site

## 2. Why Docker? The Engineer's Rationale

| **Concept** | **Explanation** |
| --- | --- |
| Isolation | Each container (WordPress, MariaDB) runs in its own isolated environment — no conflicts with host packages. |
| Portability | The same docker run command deploys WordPress identically on any Docker host — dev, staging, or production. |
| Speed | No OS install, no package compilation. Docker pulls pre-built images from Docker Hub in minutes. |
| Linked Networking | --link creates a named network alias so WordPress finds the database by name (wp-db) not IP — survives container restarts. |

## 3. Infrastructure Architecture

> **  Container Architecture** dev-app host (port 8080 → container port 80)     │     ├── Container: wp-app  (wordpress:php8.2-apache)     │       ├── Port: 8080:80  (host:container)     │       ├── DB Host: wp-db:3306  (linked alias)     │       └── Volume: wp_app_data:/var/www/html     │     └── Container: wp-db  (mariadb:11)             ├── MYSQL_DATABASE: wordpress             ├── MYSQL_USER: wpuser             └── Volume: wp_db_data:/var/lib/mysql

## 4. Step-by-Step Execution

### Step 1 — Install Docker CE

Install Docker Community Edition on dev-app from the official Docker repository.

```bash
# Install dnf plugins for repository management
sudo dnf -y install dnf-plugins-core
# Add the Docker CE repository
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# Install Docker CE, CLI, and containerd
sudo dnf -y install docker-ce docker-ce-cli containerd.io
# Enable and start Docker service
sudo systemctl enable --now docker
# Verify Docker is running
sudo systemctl status docker
```

> **  Engineer's Note** dnf-plugins-core enables config-manager which adds external repos. The Docker CE repo provides docker-ce (daemon), docker-ce-cli (client), and containerd.io (container runtime) — all three are required.

> **  Screenshot 1: dnf install docker-ce output and systemctl enable --now docker creating symlink**

### Step 2 — Create Working Directory and Deploy MariaDB Container

Create a directory for WordPress files, then start the MariaDB database container first — WordPress cannot initialize without a reachable database.

```bash
# Create project directory mkdir -p ~/wordpress && cd ~/wordpress
# Start MariaDB container (database backend)
sudo docker run -d --name wp-db \   -e MYSQL_DATABASE=wordpress \   -e MYSQL_USER=wpuser \   -e MYSQL_PASSWORD=wp_pass123 \   -e MYSQL_ROOT_PASSWORD=root_pass123 \   -v wp_db_data:/var/lib/mysql \   --restart unless-stopped \   mariadb:11
# Verify container is running
sudo docker ps
```

> **  Screenshot 2: docker run mariadb output showing image pull progress and container start**

### Step 3 — Deploy WordPress Container

Start the WordPress container, linking it to the MariaDB container and mapping host port 8080 to container port 80.

```bash
# Start WordPress container linked to MariaDB
sudo docker run -d --name wp-app \   --link wp-db:db \   -e WORDPRESS_DB_HOST=db:3306 \   -e WORDPRESS_DB_USER=wpuser \   -e WORDPRESS_DB_PASSWORD=wp_pass123 \   -e WORDPRESS_DB_NAME=wordpress \   -p 8080:80 \   -v wp_app_data:/var/www/html \   --restart unless-stopped \   wordpress:php8.2-apache
# Verify both containers running
sudo docker ps
# Access WordPress:
# http://<dev-app-IP>:8080
```

> **  Key Concept: --link and Port Mapping** --link wp-db:db creates a network alias "db" pointing to the MariaDB container. WordPress uses WORDPRESS_DB_HOST=db:3306 — resolves "db" to the container IP automatically. -p 8080:80 maps host port 8080 to container port 80 (Apache inside the WordPress container). Volume wp_app_data:/var/www/html persists WordPress files across container restarts.

> **  Screenshot 3: docker run wordpress output and docker ps showing both wp-db and wp-app running**

## 5. Verification Matrix

| **#** | **Check Item** | **How to Verify** | **Status** |
| --- | --- | --- | --- |
| 1 | dnf-plugins-core installed | dnf list installed │ grep dnf-plugins-core |  Done |
| 2 | Docker CE repository added | dnf repolist │ grep docker |  Done |
| 3 | docker-ce, docker-ce-cli, containerd.io installed | docker --version |  Done |
| 4 | Docker service enabled and running | systemctl status docker: active (running) |  Done |
| 5 | MariaDB container (wp-db) running | docker ps shows wp-db Up |  Done |
| 6 | WordPress container (wp-app) running | docker ps shows wp-app Up, port 8080->80 |  Done |
| 7 | WordPress accessible on port 8080 | http://<dev-app-IP>:8080 loads WP setup page |  Done |

## 6. Command Quick Reference

```bash
# ── INSTALL DOCKER ──────────────────────────────────────────────────────
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf -y install docker-ce docker-ce-cli containerd.io
sudo systemctl enable --now docker
# ── DEPLOY DATABASE ─────────────────────────────────────────────────────
sudo docker run -d --name wp-db -e MYSQL_DATABASE=wordpress \   -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=wp_pass123 \   -e MYSQL_ROOT_PASSWORD=root_pass123 --restart unless-stopped mariadb:11
# ── DEPLOY WORDPRESS ────────────────────────────────────────────────────
sudo docker run -d --name wp-app --link wp-db:db \   -e WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_USER=wpuser \   -e WORDPRESS_DB_PASSWORD=wp_pass123 -e WORDPRESS_DB_NAME=wordpress \   -p 8080:80 --restart unless-stopped wordpress:php8.2-apache
# ── MANAGE ──────────────────────────────────────────────────────────────
sudo docker ps
# list running containers
sudo docker logs wp-app
# view WordPress logs
sudo docker stop wp-app wp-db
# stop both containers
```

> **Ticket 67 · Deploy WordPress via Docker Containers · Procore-Plus Lab***  │  Assignee: Romain Sinclair · PROCORE Infrastructure Team*
