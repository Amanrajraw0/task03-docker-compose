# 🐳 Task 03 – Docker Compose Multi-Environment Setup

## 📘 Project Overview

This project demonstrates how to use **Docker Compose** to build a **multi-container environment** for a static website deployment using **Nginx** and **Watchtower** on an AWS EC2 instance.  
The goal of this task was to automate the web server setup, enable **live content updates**, and manage **container auto-updates** using Watchtower — showcasing real-world DevOps practices.

---

## 🏢 Organization
**NulClass Internship – DevOps Project Task 03**

**By:** Aman Raj Raw  
🎓 B.Tech CSE | 💻 DevOps & Cloud Enthusiast  
📧 amanrajraw@example.com  
🌐 GitHub: [Amanrajraw0](https://github.com/Amanrajraw0)

---

## 🧰 Tools & Technologies Used

| Tool / Technology | Purpose |
|--------------------|----------|
| **Docker** | Containerization of applications |
| **Docker Compose** | Managing multi-container environments |
| **Nginx** | Hosting and serving the static website |
| **Watchtower** | Auto-updating Docker containers |
| **AWS EC2 (Ubuntu 22.04)** | Cloud instance for deployment |
| **GitHub** | Version control and project hosting |

---

## ⚙️ Step-by-Step Implementation

### 🪜 Step 1: Launch EC2 Instance
1. Launched an **Ubuntu EC2 instance** (Free Tier `t3.micro`) in **AWS region `eu-north-1 (Stockholm)`**.  
2. Configured **Security Group**:
   - Port `22` → SSH access  
   - Port `80` → HTTP web traffic  

---

### 🧱 Step 2: Install Docker & Docker Compose

#### 🐋 Install Docker
```bash
sudo apt update -y
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
docker --version
```
### ⚙️ Install Docker Compose Plugin
```bash
sudo apt install docker-compose-plugin -y
docker compose version
```
✅ **Docker and Docker Compose installed successfully.**

## 📂 Step 3: Project Folder Structure
```bash
task03-docker-compose/
│
├── docker-compose.yml        # Multi-container setup (Nginx + Watchtower)
├── nginx/
│   ├── index.html            # Static website (auto-updates live)
│   └── nginx.conf            # Nginx configuration
├── .gitignore                # Keeps repo clean and secure
└── README.md                 # Project documentation
```
## 🧩 Step 4: Create docker-compose.yml
```bash
version: "3.8"

services:
  nginx:
    image: nginx:latest
    container_name: nginx_web
    ports:
      - "${NGINX_PORT}:80"

    volumes:
      # Mount EC2 files directly into the container
      - /home/ubuntu/task03-docker-compose/nginx/index.html:/usr/share/nginx/html/index.html
      - /home/ubuntu/task03-docker-compose/nginx/nginx.conf:/etc/nginx/nginx.conf

    environment:
      - ENVIRONMENT=${ENVIRONMENT}
    
    # Prevent Watchtower from replacing this container
    labels:
      - "com.centurylinklabs.watchtower.enable=false"

    restart: always

  watchtower:
    image: containrrr/watchtower
    container_name: watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --cleanup --interval 3600
    restart: always
```
### 🧠 Explanation

- **Nginx Container** → Serves the website from the `nginx/` folder.  
- **Watchtower Container** → Automatically checks and updates images every **1 hour**.  
- **Bind Mounts** → Ensure local HTML edits instantly reflect live on the site.  
- **Restart Policy** → Ensures containers always start automatically on **system reboot**.  
- **Watchtower Label** → Disables automatic Nginx image updates, keeping your custom changes safe.  

## 🪜 Step 5: Create Website (index.html)
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Task 03 – Docker Compose Multi-Environment</title>
</head>
<body>
  <h1>✅ Task 03 – Docker Compose Multi-Environment Setup</h1>
  <p>This static website is running inside a Docker container managed by Docker Compose.</p>
  <p>By: <b>Aman Raj Raw</b> | B.Tech CSE | DevOps & Cloud Enthusiast</p>
  <hr>
  <h3>🧠 Live Edit Demonstration</h3>
  <p>Edit this file in <code>~/task03-docker-compose/nginx/index.html</code> to see instant updates without restarting Docker.</p>
  <hr>
  <h2>🚀 Task 02 Summary (Terraform EC2 Automation)</h2>
  <p>Created EC2 instance, opened ports 80/22, and auto-installed Docker using Terraform <code>user_data</code>.</p>
  <h2>🐳 Task 03 Summary (Docker Compose Setup)</h2>
  <p>Deployed a multi-container setup (Nginx + Watchtower) with auto-update and live-edit capability.</p>
</body>
</html>
```
## 🧠 Step 6: Run the Containers
```bash
sudo docker compose up -d
```
✅ Expected Output:
```
✔ Network created
✔ Container nginx_web started
✔ Container watchtower started
```
## 🌍 Step 7: Verify Deployment
Check running containers:
```bash
sudo docker ps
```
Expected:
```bash
CONTAINER ID   IMAGE                   PORTS               NAMES
xxxxxxx        nginx:latest            0.0.0.0:80->80/tcp  nginx_web
xxxxxxx        containrrr/watchtower   8080/tcp            watchtower
```
Then open your EC2 public IP in browser:
```cpp
http://13.60.223.121/
```
✅ You’ll see your Task 03 webpage live.

## 🧩 Step 8: Live Update Demo
```bash
sudo nano ~/task03-docker-compose/nginx/index.html
```
Add a new line → save → refresh your browser.  
✅ **Changes appear instantly!**  
No `docker restart` **needed — because of bind mounts.**

## 🧹 Step 9: Cleanup and Secure Repo
Create `.gitignore` :
```bash
cat > .gitignore << 'EOF'
*.log
*.tmp
*.bak
*.swp
.docker/
.env
.aws/
*.pem
*.key
*.crt
EOF
```
Commit and push clean repo via SSH:
```bash
git add .
git commit -m "Task03 – Docker Compose Multi-Environment Setup | NulClass Internship"
git push -u origin main
```
## 📸 Proof of Work
| Step | Description | Status |
|------|--------------|--------|
| ✅ **Docker Compose Up** | Containers running successfully | ✔️ |
| 🧠 **Live HTML Edit** | Instant update verified | ✔️ |
| 🛠️ **Watchtower Logs** | Auto-update service active | ✔️ |
| 🌐 **Public Site** | Accessible via EC2 IP | ✔️ |
| 📦 **GitHub Repo** | Clean structure, no credentials | ✔️ |

## 🧠 Key Learnings

- Building multi-container environments using **Docker Compose**
- Understanding **bind mounts** for real-time file sync
- Managing auto-updates with **Watchtower**
- Deploying and maintaining containerized apps on **AWS EC2**
- Maintaining secure and professional **GitHub DevOps repositories**

---

## 🏁 Conclusion

This task demonstrates how to design and deploy an automated multi-environment setup using Docker Compose, ensuring scalability, maintainability, and real-time updates.  
It combines **automation (Task 02 – Terraform)** and **container orchestration (Task 03 – Docker Compose)** to represent a real-world DevOps workflow.

---

### 👩‍💻 Author

**Aman Raj Raw**  
🎓 *B.Tech CSE* | 💻 *DevOps & Cloud Enthusiast*  

📧 [amanrajraw0gmail.com](mailto:amanrajraw0gmail.com)  
🌐 GitHub: [Amanrajraw0](https://github.com/Amanrajraw0)

## 🙏 Acknowledgment
Thanks to **NulClass** for assigning this hands-on DevOps project.
It provided real-world exposure to containerization, automation, and modern deployment workflows.



