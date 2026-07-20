<div align="center">

# 🧃 Deploy OWASP Juice Shop using Docker Compose

### Adding a Modern Vulnerable Web App to the Cybersecurity Home Lab

![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04.4%20LTS-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Juice Shop](https://img.shields.io/badge/Juice%20Shop-OWASP%20Top%2010-FF6C37?style=for-the-badge&logo=owasp&logoColor=white)
![DVWA](https://img.shields.io/badge/DVWA-Running-red?style=for-the-badge&logo=owasp&logoColor=white)
![Status](https://img.shields.io/badge/Status-Verified%20✅-brightgreen?style=for-the-badge)

</div>

---

## 📚 Table of Contents

- [🎯 Objective](#-objective)
- [📋 Prerequisites](#-prerequisites)
- [💾 Step 1 — Backup Existing Compose File](#-step-1--backup-existing-compose-file)
- [📝 Step 2 — Edit docker-compose.yml](#-step-2--edit-docker-composeyml)
- [➕ Step 3 — Add Juice Shop Service](#-step-3--add-juice-shop-service)
- [📄 Step 4 — Final docker-compose.yml](#-step-4--final-docker-composeyml)
- [✅ Step 5 — Validate Docker Compose File](#-step-5--validate-docker-compose-file)
- [🚀 Step 6 — Deploy Juice Shop](#-step-6--deploy-juice-shop)
- [🔍 Step 7 — Verify Running Containers](#-step-7--verify-running-containers)
- [🌐 Step 8 — Verify Docker Network](#-step-8--verify-docker-network)
- [📜 Step 9 — Verify Juice Shop Logs](#-step-9--verify-juice-shop-logs)
- [🌍 Step 10 — Verify Browser Access](#-step-10--verify-browser-access)
- [🎯 Step 11 — Verify Existing Applications](#-step-11--verify-existing-applications)
- [🧾 Step 12 — Verify All Services](#-step-12--verify-all-services)
- [🏗️ Network Architecture](#️-network-architecture)
- [🔌 Port Mapping](#-port-mapping)
- [🛠️ Useful Docker Commands](#️-useful-docker-commands)
- [📊 Deployment Status](#-deployment-status)

---

## 🎯 Objective

This step adds **OWASP Juice Shop** to the existing Docker Compose project. Juice Shop is a modern, **intentionally vulnerable** web application used for learning the **OWASP Top 10** and practicing web application penetration testing.

After this deployment, the following services will run on the same Docker network:

- 🗄️ MariaDB
- 🎯 DVWA
- 🧃 Juice Shop

---

## 📋 Prerequisites

Make sure the following are already in place:

- ✅ Ubuntu Server 24.04.4 LTS
- ✅ Docker Engine Installed
- ✅ Docker Compose Installed
- ✅ CyberLab Project Created
- ✅ DVWA Running Successfully

**Current project structure:**

```text
~/cyberlab
├── backups
├── compose
│   ├── docker-compose.yml
│   ├── .env
│   └── README.md
├── docs
├── scripts
└── volumes
```

---

## 💾 Step 1 — Backup Existing Compose File

Before making any changes, it's good practice to back up the current compose file.

```bash
cd ~/cyberlab/compose

cp docker-compose.yml docker-compose.dvwa.bak
```

**Verify:**

```bash
ls
```

<details>
<summary>📤 Expected Output</summary>

```text
docker-compose.dvwa.bak
docker-compose.yml
README.md
```

</details>

---

## 📝 Step 2 — Edit docker-compose.yml

Open the compose file:

```bash
nano docker-compose.yml
```

---

## ➕ Step 3 — Add Juice Shop Service

Add the following configuration **after** the `dvwa:` service block:

```yaml
  juice-shop:
    image: bkimminich/juice-shop:latest
    container_name: juice-shop
    restart: unless-stopped

    ports:
      - "3000:3000"

    networks:
      - cyberlab-network
```

---

## 📄 Step 4 — Final docker-compose.yml

The complete file should now look like this:

```yaml
services:

  mariadb:
    image: mariadb:11.8
    container_name: mariadb
    restart: unless-stopped

    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: dvwa
      MYSQL_USER: dvwa
      MYSQL_PASSWORD: password

    volumes:
      - ../volumes/dvwa/mysql:/var/lib/mysql

    networks:
      - cyberlab-network

  dvwa:
    image: ghcr.io/digininja/dvwa:latest
    container_name: dvwa
    restart: unless-stopped

    depends_on:
      - mariadb

    environment:
      DB_SERVER: mariadb
      DB_DATABASE: dvwa
      DB_USERNAME: dvwa
      DB_PASSWORD: password

    ports:
      - "8080:80"

    networks:
      - cyberlab-network

  juice-shop:
    image: bkimminich/juice-shop:latest
    container_name: juice-shop
    restart: unless-stopped

    ports:
      - "3000:3000"

    networks:
      - cyberlab-network

networks:
  cyberlab-network:
    driver: bridge
```

**Save the file** (nano):

```
Ctrl + O   →  Enter   →  Ctrl + X
```

---

## ✅ Step 5 — Validate Docker Compose File

Check the syntax:

```bash
docker compose config
```

<details>
<summary>📤 Expected Output</summary>

```text
name: cyberlab

services:

  mariadb

  dvwa

  juice-shop

networks:

  cyberlab-network
```

</details>

> ✅ If no error appears, the compose file is valid.

---

## 🚀 Step 6 — Deploy Juice Shop

Deploy the container:

```bash
docker compose up -d
```

> ℹ️ **First deployment:** Docker will download the Juice Shop image, which may take a few minutes.

<details>
<summary>📤 Expected Output</summary>

```text
[+] Running

✔ Image bkimminich/juice-shop:latest Pulled

✔ Container juice-shop Started
```

</details>

---

## 🔍 Step 7 — Verify Running Containers

```bash
docker ps
```

<details>
<summary>📤 Expected Output</summary>

```text
CONTAINER ID   IMAGE                             STATUS

xxxxxxxxxxxx   mariadb:11.8                      Up

xxxxxxxxxxxx   ghcr.io/digininja/dvwa:latest     Up

xxxxxxxxxxxx   bkimminich/juice-shop:latest      Up
```

</details>

---

## 🌐 Step 8 — Verify Docker Network

```bash
docker network ls
```

<details>
<summary>📤 Expected Output</summary>

```text
cyberlab_cyberlab-network
```

</details>

---

## 📜 Step 9 — Verify Juice Shop Logs

```bash
docker logs juice-shop
```

<details>
<summary>📤 Expected Output (last lines)</summary>

```text
Listening on port 3000
```

or

```text
Server listening on port 3000
```

</details>

> ✅ This confirms the application started successfully.

---

## 🌍 Step 10 — Verify Browser Access

Open a browser on the **Windows host**:

```
http://192.168.56.101:3000
```

**Expected result:** the OWASP Juice Shop homepage loads.

---

## 🎯 Step 11 — Verify Existing Applications

Confirm DVWA is still working:

```
http://192.168.56.101:8080
```

**Expected result:** the DVWA login page opens.

---

## 🧾 Step 12 — Verify All Services

```bash
docker ps
```

<div align="center">

| Container | Status |
|---|:---:|
| MariaDB | 🟢 Running |
| DVWA | 🟢 Running |
| Juice Shop | 🟢 Running |

</div>

---

## 🏗️ Network Architecture

```text
                    Windows Host
                           │
        ┌──────────────────┴──────────────────┐
        │                                     │
http://192.168.56.101:8080        http://192.168.56.101:3000
        │                                     │
      DVWA                              Juice Shop
        │
        ▼
     MariaDB
        │
   cyberlab-network
        │
   Docker Engine
        │
Ubuntu Server 24.04.4 LTS
```

> 💡 **Note:** Juice Shop runs standalone with its own internal database — it does **not** connect to the MariaDB instance used by DVWA.

---

## 🔌 Port Mapping

<div align="center">

| Service | Container Port | Host Port |
|---|:---:|:---:|
| 🎯 DVWA | 80 | 8080 |
| 🧃 Juice Shop | 3000 | 3000 |
| 🗄️ MariaDB | Internal Only | Not Published |

</div>

---

## 🛠️ Useful Docker Commands

| Command | Description |
|---|---|
| `docker ps` | Show running containers |
| `docker logs juice-shop` | View Juice Shop logs |
| `docker logs dvwa` | View DVWA logs |
| `docker compose stop` | Stop all services |
| `docker compose start` | Restart existing containers |
| `docker compose up -d` | Start the full lab |

---

## 📊 Deployment Status

<div align="center">

| Component | Status |
|---|:---:|
| Docker Engine | ✅ Installed |
| Docker Compose | ✅ Installed |
| MariaDB | ✅ Running |
| DVWA | ✅ Running |
| Juice Shop | ✅ Running |
| Docker Network | ✅ Created |
| Browser Access | ✅ Working |
| Ready for Pentesting | ✅ Completed |

</div>

---

<div align="center">

**🔐 Part of the Cybersecurity Home Lab Series**

*Next up: adding WebGoat, crAPI, and MailHog to `cyberlab-network` 🚀*

</div>
