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

This guide helps set up a complete local DevOps project using **Terraform** and **Docker** on **Ubuntu**. You will create a simple Nginx container using Terraform.

## 🔗 Terraform Analogy Story: Let's Build a House with Code

---

### 🔗 Imagine this:

There's an architect named **Terra** (Terraform). Terra is hired by many companies to build houses (infrastructure). But Terra doesn’t carry bricks or tools. Instead, Terra writes **blueprints** on paper using a special language. This language is called **HCL** (HashiCorp Configuration Language).

#### 🔗 The Blueprint

Terra writes in her blueprint:

> "I want one small house with 2 rooms, a front door, and a garden. Place it in City A."

This blueprint is just a **.tf file**.

---

### 🔗 Terra's Builders (Providers)

Terra cannot build things by herself. She needs a **provider** (like a construction company) to do the work.

She can choose:

* **Docker Inc.** to build containers
* **AWS Ltd.** to build cloud servers
* **Google Constructors** to set up storage

For this story, let’s say Terra hires **Docker Inc.** to build a containerized home.

---

### 🔗 How Construction Happens

1. Terra writes the blueprint.
2. She runs: `terraform init` — this tells the provider to get ready.
3. She runs: `terraform apply` — the house starts getting built.
4. If something goes wrong or the house is no longer needed: `terraform destroy` — and it vanishes.

Even better? If the blueprint changes ("add a 3rd room"), Terra can **update** the existing house automatically. She doesn’t need to rebuild from scratch!

---

### 🔗 Why is Terra So Smart?

Because she:

* **Keeps state**: Terra remembers what she built.
* **Is idempotent**: If you run the same plan twice, it won’t build twice — it knows nothing changed.
* **Is predictable**: You always see the plan before building (via `terraform plan`).

---

## 🔗 Terraform vs Kubernetes — Who Does What?

| Tool       | Role                                       |
| ---------- | ------------------------------------------ |
| Terraform  | Architect + Builder (infrastructure setup) |
| Docker     | Container Maker (packages your app)        |
| Kubernetes | Manager (runs your app & scales it)        |

---

## 🔗 Mermaid Diagram — Terraform Workflow

<pre> ```
Write main.tf (blueprint)
        ↓
   terraform init
        ↓
   terraform plan
        ↓
   terraform apply
        ↓
Docker Container / Cloud Infra Ready
        ↓
Use infra for app or deployment
        ↓
terraform destroy (if needed)
``` </pre>

---

## 🔗 In short

So next time you think about Terraform, remember:

* Terraform is **Terra the architect**.
* She doesn’t build with her hands — she writes blueprints.
* Those blueprints turn into real houses (infrastructure) using builders (providers).

You control your infrastructure just like building a LEGO house — with instructions, updates, and undo power — all from your keyboard!


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
