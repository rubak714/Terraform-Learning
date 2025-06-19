# Terraform + Docker Project on Ubuntu (without AWS)

## 🔗 What is Terraform?

**Terraform** is a tool that helps you create and manage infrastructure using code. This method is called **Infrastructure as Code (IaC)**.

### 🔗 Why use Terraform?

* Instead of clicking buttons or setting up servers manually, you write a file (like a recipe) that describes what you want.
* Terraform reads that file and creates everything for you.
* You can also destroy or change the setup by changing the file.

### 🔗 What can Terraform do?

* Create Docker containers
* Deploy servers on AWS, Azure, GCP
* Set up Kubernetes clusters
* Manage networks, storage, and more

## 🔗 Key Terms

| Term                  | Meaning                                                      |
| --------------------- | ------------------------------------------------------------ |
| **Provider**          | The service Terraform talks to (like Docker, AWS, etc.)      |
| **Resource**          | The thing you want to create (like a container or a server)  |
| **main.tf**           | The file where you write Terraform instructions              |
| **terraform init**    | Prepares Terraform in the folder (downloads needed plugins)  |
| **terraform apply**   | Applies the setup — it creates or updates the infrastructure |
| **terraform destroy** | Destroys the infrastructure that was created                 |

---

This guide helps you set up a complete local DevOps project using **Terraform** and **Docker** on **Ubuntu**. You will create a simple Nginx container using Terraform.

---

## 🔗 Prerequisites

* Ubuntu (latest)
* Docker installed
* Minikube (optional, not used in this project)
* Terraform installed

---

## 🔗 Check if Docker is Installed

```bash
which docker
```

If installed, it returns something like:

```bash
/usr/bin/docker
```

To check Docker is running:

```bash
docker --version
docker ps
```

If Docker is not running:

```bash
sudo systemctl start docker
```

To enable Docker on reboot:

```bash
sudo systemctl enable docker
```

---

## 🔗 Check if Minikube is Installed (optional)

```bash
which minikube
```

If you are not using Kubernetes in this project, you can skip Minikube.

---

## 🔗 Check if Terraform is Installed

```bash
which terraform
```

If installed, it returns something like:

```bash
/usr/local/bin/terraform
```

To see the version:

```bash
terraform version
```

---

## 🔗 Step-by-Step Project Setup

### 1. Create Project Directory

```bash
mkdir terraform-docker-nginx
cd terraform-docker-nginx
```

### 2. Create Terraform Configuration File

Create a file named `main.tf`:

```hcl
provider "docker" {
  host = "unix:///var/run/docker.sock"
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  name  = "my-nginx"
  image = docker_image.nginx.latest
  ports {
    internal = 80
    external = 8080
  }
}
```

### 3. Initialize Terraform

```bash
terraform init
```

### 4. Apply Configuration

```bash
terraform apply
```

Type `yes` when prompted.

### 5. Verify Nginx is Running

```bash
docker ps
```

You should see `my-nginx` container running on port `8080`.

Open browser:

```
http://localhost:8080
```

---

## 🔗 After Reboot — What to Run Again

After restarting your Ubuntu system:

1. **Start Docker** (if not auto-started):

```bash
sudo systemctl start docker
```

2. **Go to Project Directory:**

```bash
cd terraform-docker-nginx
```

3. **Reapply Terraform (if container is not running):**

```bash
terraform apply
```

Or if already applied earlier, just start container manually:

```bash
docker start my-nginx
```

---

## 🔗 To Destroy the Project

```bash
terraform destroy
```

Type `yes` to confirm.

---

## 🔗 Summary

You now have a local DevOps project that:

* Uses **Terraform** to define infrastructure
* Uses **Docker** to deploy an Nginx container or your Python app
* Runs locally, no cloud needed

Let me know if you want to extend this with Flask app, Prometheus monitoring, or CI/CD with Jenkins.

---

## 🔗 Realtime Example: Python App with Multiple Scripts

Let’s say you have a Python-based backend project with the following structure:

```
python-app/
├── app/
│   ├── __init__.py
│   ├── routes.py
│   ├── models.py
│   └── utils.py
├── requirements.txt
├── run.py
└── Dockerfile
```

### 🔗 Example Python Code

**run.py**

```python
from app.routes import app

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

**routes.py**

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Welcome to the Python App!"
```

**requirements.txt**

```
flask
```

**Dockerfile**

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "run.py"]
```

### 🔗 Update Your Terraform `main.tf`

```hcl
provider "docker" {
  host = "unix:///var/run/docker.sock"
}

resource "docker_image" "python_app" {
  name         = "python-app"
  build {
    context    = "."
    dockerfile = "Dockerfile"
  }
}

resource "docker_container" "python_app" {
  name  = "my-python-app"
  image = docker_image.python_app.latest
  ports {
    internal = 5000
    external = 5000
  }
}
```

Place this `main.tf` file in the same root folder as your Python app.

### 🔗 Run Your App

```bash
terraform init
terraform apply
```

Visit:

```
http://localhost:5000
```

You should see: `Welcome to the Python App!`
