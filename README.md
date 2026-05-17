# 🚀 AWS EC2 Flask Application Deployment

## 📌 Project Overview

This project started as a basic Flask application deployment on an AWS EC2 instance and was later upgraded into a production-style backend architecture using Gunicorn, Nginx, and systemd.

The goal of this project was not just to host an application, but to understand how real-world backend services are deployed, managed, and exposed securely over the internet.

---

# 🏗️ Architecture Evolution

## 🔹 Phase 1 — Basic Deployment

User → EC2 → Flask Development Server

In the initial setup:
- Flask application was directly exposed to the internet
- Application was started manually from the terminal
- App stopped whenever the SSH session ended

This phase helped in understanding:
- EC2
- Linux basics
- SSH access
- Python virtual environments
- Security Groups
- Public networking

---

## 🔹 Phase 2 — Production-Style Deployment

User → Nginx → Gunicorn → Flask Application

The architecture was later upgraded using:
- Gunicorn as the production-grade WSGI server
- Nginx as a reverse proxy
- systemd for automatic background service management

This improved:
- reliability
- process management
- scalability
- architecture separation

---

# 🔧 Technologies Used

- Amazon EC2
- Ubuntu Linux
- Python 3
- Flask
- Gunicorn
- Nginx
- systemd
- Git & GitHub
- SSH
- Python Virtual Environment (venv)

---

# 🌐 Live Application

http://54.224.31.35/

---

# 📁 Project Structure

```text
aws-ec2-flask-app/
├── app.py
├── requirements.txt
├── README.md
└── .gitignore
```

---

# ⚙️ Deployment Workflow

---

## 🔹 1. Launch EC2 Instance

Created an Ubuntu EC2 instance on AWS and configured Security Groups:

| Port | Purpose |
|---|---|
| 22 | SSH Access |
| 80 | HTTP Web Traffic |

---

## 🔹 2. Connect via SSH

```bash
ssh -i your-key.pem ubuntu@your-public-ip
```

### Why SSH?
SSH provides secure remote access to Linux servers using encrypted communication and key-based authentication.

---

## 🔹 3. Install Python & Required Packages

```bash
sudo apt update
sudo apt install python3-pip python3-venv nginx -y
```

### Why?
- `apt` → Ubuntu package manager
- `python3-pip` → installs Python packages
- `python3-venv` → creates isolated Python environments
- `nginx` → web server & reverse proxy

---

## 🔹 4. Create Python Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
```

### Why Virtual Environment?
Virtual environments isolate project dependencies from the system Python installation.

This prevents:
- dependency conflicts
- system package corruption
- version mismatch issues

---

## 🔹 5. Install Flask & Gunicorn

```bash
pip install flask gunicorn
```

### Why Flask?
Flask is a lightweight Python web framework used to build backend applications.

### Why Gunicorn?
Gunicorn is a production-grade WSGI server that efficiently serves Python web applications.

It replaces Flask's built-in development server.

---

## 🔹 6. Initial Flask Development Deployment

```bash
sudo venv/bin/python app.py
```

### Limitation
The application stopped whenever:
- terminal closed
- SSH disconnected
- server rebooted

This highlighted the need for a proper process manager.

---

# 🔥 Production Architecture Upgrade

---

## 🔹 7. Run Gunicorn Internally

```bash
gunicorn --bind 127.0.0.1:8000 app:app
```

---

### Understanding this Command

| Part | Meaning |
|---|---|
| `gunicorn` | starts Gunicorn server |
| `127.0.0.1` | local/internal communication only |
| `8000` | internal application port |
| `app:app` | `filename:Flask_object` |

---

### Why bind to 127.0.0.1?

Because Gunicorn should NOT be exposed directly to the internet.

Only Nginx should communicate with Gunicorn internally.

---

## 🔹 8. Configure Nginx Reverse Proxy

Created configuration file:

```bash
sudo nano /etc/nginx/sites-available/flask-app
```

### Nginx Configuration

```nginx
server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

# 🧠 Understanding the Nginx Configuration

---

## `listen 80`

Nginx listens for incoming HTTP traffic on port 80.

---

## `location /`

Defines routing behavior for all incoming requests.

---

## `proxy_pass`

```nginx
proxy_pass http://127.0.0.1:8000;
```

This forwards incoming traffic to Gunicorn running internally.

---

## `proxy_set_header Host $host`

Passes original hostname information to backend application.

---

## `proxy_set_header X-Real-IP $remote_addr`

Passes the client's actual IP address to the Flask application.

---

### Why use Nginx?

Nginx acts as:
- reverse proxy
- traffic manager
- frontend web server

Benefits:
- improved performance
- better request handling
- HTTPS support
- security layer
- load balancing capability

---

## 🔹 9. Configure systemd Service

Created service file:

```bash
sudo nano /etc/systemd/system/flask-app.service
```

---

## systemd Service Configuration

```ini
[Unit]
Description=Gunicorn instance for Flask App
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/flask-app
Environment="PATH=/home/ubuntu/flask-app/venv/bin"
ExecStart=/home/ubuntu/flask-app/venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8000 app:app

[Install]
WantedBy=multi-user.target
```

---

# 🧠 Understanding the systemd Service File

---

## `[Unit]`

Defines service metadata and startup dependencies.

```ini
After=network.target
```

Means:
Start this service only after networking is available.

---

## `[Service]`

Defines how the application runs.

---

## `WorkingDirectory`

```ini
WorkingDirectory=/home/ubuntu/flask-app
```

Tells systemd where the application exists.

---

## `Environment`

```ini
Environment="PATH=/home/ubuntu/flask-app/venv/bin"
```

Ensures Gunicorn uses the virtual environment packages.

---

## `ExecStart`

```ini
ExecStart=/home/ubuntu/flask-app/venv/bin/gunicorn --workers 3 --bind 127.0.0.1:8000 app:app
```

Defines the exact command used to start the application.

---

## `--workers 3`

Creates multiple Gunicorn worker processes for handling concurrent requests.

---

## `[Install]`

```ini
WantedBy=multi-user.target
```

Ensures service starts automatically during system boot.

---

# 🧠 Key Learnings

- AWS EC2 infrastructure provisioning
- Linux server administration
- SSH remote access
- Python dependency management
- Reverse proxy architecture
- Production-grade application serving
- Linux process/service management
- Internal vs external networking
- Backend deployment workflow

---

# ⚠️ Challenges Faced

### 1. PEP 668 / Externally Managed Python Environment
Resolved using Python virtual environments.

---

### 2. Port Binding & Privileged Ports
Learned why ports below 1024 require elevated privileges.

---

### 3. Flask Development Server Limitation
Migrated from Flask dev server to Gunicorn production server.

---

### 4. Process Persistence
Solved application shutdown issue using systemd services.

---

# 🚀 Future Improvements

- HTTPS using SSL/TLS
- Custom domain configuration
- Docker containerization
- CI/CD automation
- Infrastructure as Code using Terraform
- Monitoring & logging integration

---

# 👨‍💻 Author

Mohammed Jahangir