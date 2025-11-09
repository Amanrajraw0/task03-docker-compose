# 🧩 Task 04 – GitHub → Jenkins Webhook Integration

![CI/CD](https://img.shields.io/badge/CI%2FCD-Jenkins-D24939?style=for-the-badge&logo=jenkins)
![Source](https://img.shields.io/badge/Source-GitHub-181717?style=for-the-badge&logo=github)
![Cloud](https://img.shields.io/badge/Cloud-AWS%20EC2-FF9900?style=for-the-badge&logo=amazonaws)
![Automation](https://img.shields.io/badge/Automation-Webhook-blue?style=for-the-badge&logo=githubactions)
![Region](https://img.shields.io/badge/Region-Europe%20(Stockholm)%20eu--north--1-007ACC?style=for-the-badge)
![Type](https://img.shields.io/badge/Instance%20Type-t3.micro-2ECC71?style=for-the-badge)

---

## 📘 Project Overview

This project demonstrates how to integrate **GitHub** with **Jenkins** using a **webhook**, so that every time new code is pushed to the `main` branch, Jenkins automatically triggers a build, pulls the updated code, and redeploys the Docker containers on the **AWS EC2 instance**.  

It builds upon **Task 03 (Docker Compose Multi-Environment Setup)** and introduces **Continuous Integration (CI)** automation, ensuring a real-world DevOps pipeline where deployments happen automatically on code changes.

---

## 🏢 Organization
**NulClass Internship – DevOps Project Task 04**

**By:** Aman Raj Raw  
🎓 B.Tech CSE | 💻 DevOps & Cloud Enthusiast  
📧 amanrajraw@example.com  
🌐 GitHub: [Amanrajraw0](https://github.com/Amanrajraw0)

---

## 🧰 Tools & Technologies Used

| Tool / Technology | Purpose |
|--------------------|----------|
| **GitHub** | Source code hosting and webhook trigger |
| **Jenkins** | Continuous Integration automation server |
| **AWS EC2 (Ubuntu 22.04)** | Cloud environment for hosting Jenkins & Docker |
| **Docker & Docker Compose** | Containerized application deployment |
| **Nginx** | Static website hosting (from Task 03) |
| **GitHub PAT (Personal Access Token)** | Secure authentication for Jenkins |
| **Webhook** | Automated trigger between GitHub and Jenkins |

---

## ⚙️ Step-by-Step Implementation
## 📂 Folder Structure
```markdown
task03-docker-compose/
│── docker-compose.yml
│── nginx/
│   ├── index.html
│   └── nginx.conf
│── README.md            ← Task 03 Documentation
│
└── task04/
    ├── README.md        ← Task 04 (Webhook Integration)
    ├── jenkins-job.sh   ← Jenkins build shell script (optional)
    └── screenshots/     ← Proof of webhook setup (optional)

```
## 🖼️ Architecture Diagram (Visual Overview)
```markdown
   ┌─────────────┐
   │   Developer │
   └──────┬──────┘
          │ git push
          ▼
   ┌─────────────┐
   │   GitHub    │
   └──────┬──────┘
          │ Webhook
          ▼
   ┌─────────────┐
   │   Jenkins   │
   └──────┬──────┘
          │ git pull + docker restart
          ▼
   ┌─────────────┐
   │   AWS EC2   │
   └─────────────┘
   (Live Updated Website)
```

### 🪜 Step 1: Launch EC2 Instance

- **Instance Type:** Ubuntu 22.04 LTS (Free Tier `t3.micro`)  
- **Region:** `eu-north-1 (Stockholm)`  
- **Security Group Rules:**
  - Port `22` → SSH Access  
  - Port `80` → HTTP (Website)  
  - Port `8080` → Jenkins Dashboard + Webhook  

SSH into your instance:
```bash
ssh -i "your-key.pem" ubuntu@<EC2-Public-IP>
```
### 🧱 Step 2: Install Jenkins & Docker
🧩 Install Java and Jenkins.
```bash
sudo apt update -y
sudo apt install -y openjdk-17-jdk git
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update -y
sudo apt install -y jenkins
```
🐳 Install Docker and Compose.
```bash
sudo apt install -y docker.io docker-compose-plugin
sudo systemctl enable jenkins docker
sudo systemctl start jenkins docker
```
- Access Jenkins via browser:
```bash
http://<EC2-Public-IP>:8080
```
### 🧰 Step 3: Configure GitHub Credentials in Jenkins
1. Navigate to **Manage Jenkins → Credentials → Global → Add Credentials**

2. Choose **Username and Password** type

3. Enter:
   - **Username:** Your GitHub username  
   - **Password:** Your GitHub PAT

4. **PAT Scopes required:**
   - `repo`
   - `admin:repo_hook`

5. Set **ID:** `github-jenkins`

6. **Save** ✅
---


### ⚙️ Step 4: Create Jenkins Freestyle Job

**Job Name:** `task04-webhook-integration`

---

#### 🔹 **Source Code Management**
- Select **Git**
- **Repository URL →** SSH link of your repo

```bash
git@github.com:Amanrajraw0/task03-docker-compose.git
```
- Credentials → `github-jenkins`
- Branch → `main`

**🔹 Build Trigger**

✅ Check → GitHub hook trigger for GITScm polling

#### **🔹 Build Step (Execute Shell)**
```bash
cd /home/ubuntu/task03-docker-compose
git pull origin main
sudo docker compose down
sudo docker compose up -d
echo "✅ Task 04 – Auto Deploy Completed Successfully!"
```
Click **Save**

### 🔗 Step 5: Configure GitHub Webhook
In your GitHub repository:

1. Go to Settings → Webhooks → Add Webhook

2. Enter:

-   Payload URL: http://<EC2-Public-IP>:8080/github-webhook/

- Content Type: application/json

- SSL Verification: Disabled (HTTP only)

- Events: Just the push event
3. Click Add Webhook.\
✅ Expected Result:\
-  Last delivery: Response 200 — OK

### 🧱 Step 6: Adjust Jenkins Security (Webhook Access)
**Go to:**
```pgsql
Manage Jenkins → Configure Global Security
```
### ⚙️ Set:

| **Setting**          | **Value**                                      |
|----------------------|------------------------------------------------|
| **Authorization**    | **Anyone can do anything** *(testing only)*    |
| **CSRF Protection**  | Default Crumb Issuer                           |
| **Proxy Compatibility** | ✅ Enabled                                |

---

### 🔒 After webhook success, revert to:
> “**Logged-in users can do anything**” for security.

## 🚀 Step 7: Test Webhook Trigger

Make a small change (for example, edit `index.html`) and push:

```bash
git add .
git commit -m "Webhook test: Auto trigger"
git push origin main
```
✅ Jenkins will automatically trigger the build:
```bash
Started by GitHub push by Amanrajraw0
Building on Jenkins...
Pulling latest changes...
Redeploying Docker containers...
✅ Deployment successful!
```
### 🧠 Explanation
- Webhook Trigger → GitHub sends push event to Jenkins

- Jenkins Job → Automatically pulls latest code

- Docker Compose → Rebuilds and restarts containers

- Live EC2 Website → Reflects latest updates instantly
## 📸 Proof of Work

| **Step**               | **Description**                             | **Status** |
|-------------------------|---------------------------------------------|-------------|
| ✅ **Webhook Setup**     | GitHub → Jenkins trigger successful         | ✔️          |
| 🧠 **Auto Build**        | Triggered by `git push`                     | ✔️          |
| 🐳 **Docker Redeploy**   | Containers restarted automatically          | ✔️          |
| 🌐 **Website Updated**   | EC2 site live with new content              | ✔️          |
| 🔒 **Credentials Secure**| PAT safely stored in Jenkins                | ✔️          |

### 🧠 Key Learnings
- Integrating GitHub with Jenkins using webhooks

- Configuring secure PAT-based authentication

- Managing Jenkins security & CSRF settings

- Automating Docker deployments after code updates

- Implementing CI/CD on AWS EC2
### 🏁 Conclusion

This task implements a complete **Continuous Integration (CI)** pipeline that connects GitHub and Jenkins.
Every push automatically redeploys your Docker-based application, ensuring zero manual intervention and a seamless **DevOps automation workflow.**

### 👩‍💻 Author
#### 👨‍💻 Aman Raj Raw  

🎓 *B.Tech CSE* | 💻 *DevOps & Cloud Enthusiast*  

📧 **Email:** [amanrajraw0@gmail.com](mailto:amanrajraw0@gmail.com)  
🌐 **GitHub:** [Amanrajraw0](https://github.com/Amanrajraw0)

### 🙏 Acknowledgment
Thanks to **NulClass** for assigning this hands-on **DevOps project**.\
This task helped gain practical experience in **CI/CD**, Jenkins integration, and a**utomated cloud deployment.**


