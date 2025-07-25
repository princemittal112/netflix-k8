# Netflix Clone Deployment using Kubernetes, Podman, Jenkins & GitHub

This project demonstrates the CI/CD deployment of a Netflix Clone application using Kubernetes (Kind), Podman, Jenkins, and GitHub on a macOS system. The pipeline automates image building, pushing, and deployment to a local Kubernetes cluster.

---

## 🧰 Tech Stack Used

* **Podman** for containerization
* **Kind** (Kubernetes in Docker) for a local Kubernetes cluster
* **Jenkins** for CI/CD automation
* **GitHub** for version control
* **macOS** (Apple Silicon) as the local machine

---

## 📁 Project Structure

```
Netflix-Clone-K8s-Jenkins/
├── Jenkinsfile
├── Dockerfile
├── deployment.yaml
├── service.yaml
├── .podman/
│   └── netflix-clone:latest.tar
└── src/
    └── [Netflix Clone App Source Code]
```

---

## 🚀 Jenkins Pipeline 

### 📜 Jenkinsfile

```groovy
pipeline {
  agent any

  environment {
    PODMAN = "/opt/homebrew/bin/podman"
    TMDB_API_KEY = "5c8d658e9e63671c75cf66a8176ab391"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/princemittal112/netflix-k8.git'
      }
    }

    stage('Build Image') {
      steps {
        sh '/opt/homebrew/bin/podman build -t mittalprince2005/netflix-clone:latest --build-arg TMDB_API_KEY=5c8d658e9e63671c75cf66a8176ab391 .'
      }
    }

    stage('Push Image') {
      steps {
        sh '/opt/homebrew/bin/podman push mittalprince2005/netflix-clone:latest'
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '/opt/homebrew/bin/kubectl apply -f deployment.yaml'
        sh '/opt/homebrew/bin/kubectl apply -f service.yaml'
      }
    }

    stage('Port Forward') {
      steps {
        sh 'nohup /opt/homebrew/bin/kubectl port-forward svc/netflix-clone 8082:80 &'
      }
    }

    stage('Check Status') {
      steps {
        sh '/opt/homebrew/bin/kubectl get pods'
        sh '/opt/homebrew/bin/kubectl get svc'
      }
    }
  }

  post {
    success {
      echo 'Netflix clone deployed successfully via Jenkins.'
    }
    failure {
      echo 'Jenkins pipeline failed.'
    }
  }
}
```

---

## 📄 Kubernetes Files

### `deployment.yaml`

Defines the deployment of the Netflix app container.

### `service.yaml`

Exposes the deployment as a service.

---

## 🌐 Accessing the Application

```bash
kubectl port-forward svc/netflix-clone-service 6060:80
```

Then open: `http://localhost:6060`

---

## 📌 Notes

* Ensure the Jenkins agent has access to Podman and kubectl.
* SSH issues must be resolved by configuring the correct key permissions and paths.
* Kind must be running and reachable.

---

## 📸 Screenshots (optional)

<img width="1440" height="900" alt="Screenshot 2025-07-22 at 1 16 27 PM" src="https://github.com/user-attachments/assets/6d977198-d161-4a66-9127-12ce6a36190b" />

<img width="1440" height="900" alt="Screenshot 2025-07-22 at 1 16 30 PM" src="https://github.com/user-attachments/assets/359ad291-372c-4cc4-af40-763a1b907752" />

![WhatsApp Image 2025-07-23 at 19 26 42](https://github.com/user-attachments/assets/91f7dd99-317a-4d03-b0ce-2bcf44541bc5)

![WhatsApp Image 2025-07-23 at 19 26 54](https://github.com/user-attachments/assets/fbaa2748-0233-4f0f-b636-10d6bb8e2884)

