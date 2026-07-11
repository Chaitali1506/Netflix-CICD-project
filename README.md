# Netflix Clone - DevSecOps CI/CD Pipeline

##  Project Overview

This project demonstrates a complete DevSecOps CI/CD pipeline for deploying a Netflix Clone application. The pipeline automates the entire software delivery process—from source code checkout to deployment on a Kubernetes cluster—while integrating security and quality checks at every stage.

The objective of this project is to showcase practical implementation of DevSecOps tools and best practices used in real-world software development.

---

## Features

- Automated CI/CD pipeline using Jenkins
- Source code management with GitHub
- Static Code Analysis using SonarQube
- Filesystem Vulnerability Scan using Trivy
- Docker Image Build
- Docker Image Vulnerability Scan
- Docker Image Push to Docker Hub
- Kubernetes (K3s) Deployment
- Email notification after pipeline execution
- Automated deployment with minimal manual intervention

---

## Tech Stack

| Category | Tools |
|----------|-------|
| Version Control | Git, GitHub |
| CI/CD | Jenkins |
| Code Quality | SonarQube |
| Security Scanning | Trivy |
| Containerization | Docker |
| Container Registry | Docker Hub |
| Orchestration | Kubernetes (K3s) |
| Frontend | HTML, CSS, JavaScript |
| Operating System | Ubuntu |

---

##  Project Structure

```
Netflix-Clone/
│
├── Jenkinsfile
├── index.html
├── index.css
├── index.js
└── README.md
```

---

## CI/CD Pipeline Workflow

```
GitHub
   │
   ▼
Jenkins Pipeline
   │
   ├── Checkout Source Code
   ├── SonarQube Code Analysis
   ├── Trivy Filesystem Scan
   ├── Build Docker Image
   ├── Trivy Docker Image Scan
   ├── Push Image to Docker Hub
   ├── Deploy to Kubernetes (K3s)
   └── Send Email Notification
```

---

##  Pipeline Stages

### 1. Source Code Checkout
The latest code is pulled automatically from the GitHub repository.

### 2. SonarQube Analysis
Performs static code analysis to identify bugs, code smells, and maintainability issues.

### 3. Trivy Filesystem Scan
Scans the project files for known vulnerabilities before building the Docker image.

### 4. Docker Image Build
Builds a Docker image of the application.

### 5. Docker Image Scan
Scans the generated Docker image using Trivy for vulnerabilities.

### 6. Push to Docker Hub
Uploads the verified Docker image to Docker Hub.

### 7. Kubernetes Deployment
Deploys the application to a K3s Kubernetes cluster.

### 8. Email Notification
Sends build status notifications after every pipeline execution.

---

##  Prerequisites

Before running this project, install:

- Git
- Docker
- Jenkins
- SonarQube
- Trivy
- kubectl
- K3s Kubernetes
- Docker Hub Account

---

##  How to Run

### Clone Repository

```bash
git clone https://github.com/your-username/Netflix-Clone.git
cd Netflix-Clone
```

### Configure Jenkins

- Install required plugins
- Configure SonarQube Server
- Add Docker Hub Credentials
- Configure Kubernetes access
- Create Jenkins Pipeline Job

### Run Pipeline

Click **Build Now** in Jenkins.

The pipeline will automatically:

- Checkout code
- Analyze code quality
- Scan vulnerabilities
- Build Docker image
- Push image to Docker Hub
- Deploy to Kubernetes
- Send email notification

---

##  Access Application

After successful deployment, open:

```
http://<Jenkins-IP>:30080
```

Example:

```
http://192.168.1.100:30080
```

---

## Security Checks

✔ SonarQube Static Code Analysis

✔ Trivy Filesystem Scan

✔ Trivy Docker Image Scan

These checks help identify security vulnerabilities before deployment.

---

## Learning Outcomes

Through this project, I gained practical experience in:

- Building CI/CD pipelines
- DevSecOps practices
- Docker containerization
- Kubernetes deployment
- Security scanning using Trivy
- Code quality analysis using SonarQube
- Jenkins pipeline automation

---

## Author

**Chaitali Kale**

Aspiring DevSecOps Engineer passionate about CI/CD automation, Cloud, Docker, Kubernetes, and DevSecOps.

---
