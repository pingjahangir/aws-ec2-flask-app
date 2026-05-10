# 🚀 AWS EC2 Flask Application Deployment

## 📌 Project Overview

This project demonstrates how to deploy a Python Flask web application on an AWS EC2 instance using a Linux-based cloud environment.

The application is hosted on Ubuntu running inside Amazon EC2 and is publicly accessible over HTTP.

---

## 🏗️ Architecture

User → Internet → Security Group → EC2 → Flask Application

---

## 🔧 Technologies Used

- Amazon EC2
- Ubuntu Linux
- Python 3
- Flask
- Git & GitHub
- SSH
- Python Virtual Environment (venv)

---

## 🌐 Live Application

http://54.224.31.35/

---

## 📁 Project Structure

```text
aws-ec2-flask-app/
├── app.py
├── requirements.txt
├── README.md
└── .gitignore
```

---

## ⚙️ Deployment Workflow

### 1. Launch EC2 Instance
- Created Ubuntu EC2 instance on AWS
- Configured Security Groups:
  - SSH (22)
  - HTTP (80)

### 2. Connect to EC2 via SSH

```bash
ssh -i your-key.pem ubuntu@your-public-ip
```

### 3. Install Dependencies

```bash
sudo apt update
sudo apt install python3-pip python3-venv -y
```

### 4. Create Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
```

### 5. Install Flask

```bash
pip install flask
```

### 6. Run Flask Application

```bash
sudo venv/bin/python app.py
```

---

## 🧠 Key Learnings

- Understanding AWS EC2 infrastructure
- Secure remote access using SSH
- Linux package management with apt
- Python dependency isolation using venv
- Network traffic handling using Security Groups
- Hosting backend applications on cloud servers

---

## ⚠️ Challenges Faced

### 1. Externally Managed Python Environment (PEP 668)

While installing Flask globally using pip, Ubuntu restricted package installation to protect system Python.

### Solution
Implemented Python virtual environments (venv) for isolated dependency management.

---

### 2. Understanding Public vs Private IPs

Learned the distinction between:
- Public IP → internet accessible
- Private IP → internal AWS network communication

---

### 3. Flask Development Server Limitation

Understood that Flask’s built-in development server is not suitable for production-grade deployments.

---

## ⚠️ Current Limitations

- Application stops if terminal session closes
- Flask development server is not production-ready
- No reverse proxy configured yet
- No process manager configured

---

## 🚀 Future Improvements

- Deploy using Gunicorn
- Configure Nginx reverse proxy
- Implement HTTPS using SSL/TLS
- Dockerize the application
- Automate deployment using CI/CD pipelines

---

## 👨‍💻 Author

Mohammed Jahangir
