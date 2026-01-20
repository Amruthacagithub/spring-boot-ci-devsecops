# HealthService – CI/CD DevSecOps Project

## Project Description

HealthService is a simple Spring Boot application used to demonstrate a **complete CI/CD pipeline** using **GitHub Actions**, following **DevSecOps best practices**.
The pipeline includes code quality checks, security scanning, Docker image creation, and Kubernetes deployment.

---

## How to Run Locally

### Prerequisites

Make sure the following are installed:

* Java 17
* Maven
* Docker
* Git

Verify:

```bash
java -version
mvn -version
docker --version
```

---

### Run Without Docker

1. Build the application:

```bash
mvn clean package
```

2. Run the JAR:

```bash
java -jar target/healthservice-0.0.1-SNAPSHOT.jar
```

3. Access the app:

```
http://localhost:8080
http://localhost:8080/actuator/health
```

---

### Run Using Docker

1. Build Docker image:

```bash
docker build -t healthservice:local .
```

2. Run container:

```bash
docker run -d -p 8080:8080 --name healthservice healthservice:local
```

3. Verify:

```bash
docker ps
docker logs healthservice
```

---

## Secrets Configuration (Mandatory)

The CI pipeline pushes Docker images to DockerHub.
Credentials are stored securely using **GitHub Secrets**.

### Required Secrets

Add the following in:

**GitHub Repository → Settings → Secrets and variables → Actions**

| Secret Name        | Purpose                |
| ------------------ | ---------------------- |
| DOCKERHUB_USERNAME | DockerHub username     |
| DOCKERHUB_TOKEN    | DockerHub access token |

⚠️ **Important**:

* Secrets are NOT hardcoded
* Pipeline fails if secrets are missing
* Using tokens is mandatory (password not allowed)

---

## CI Pipeline Explanation

The CI pipeline is defined in:

```
.github/workflows/ci.yml
```

### Pipeline Trigger

* Runs on every push to `master` or `main`
* Can also be triggered manually

---

### CI Stages

1. **Checkout Code**
   Retrieves the latest source code from GitHub.

2. **Setup Java**
   Installs Java 17 to ensure consistent build environment.

3. **Checkstyle (Code Quality)**
   Enforces Java coding standards using `google_checks.xml`.

4. **CodeQL (SAST)**
   Performs static application security testing to detect code-level vulnerabilities.

5. **Build Application**
   Compiles and packages the Spring Boot application using Maven.

6. **Docker Build & Push**

   * Builds Docker image
   * Logs in securely to DockerHub
   * Pushes trusted image using GitHub Secrets

---

## Kubernetes Deployment (CD)

After a successful CI pipeline:

1. Kubernetes manifests are applied:

```bash
kubectl apply -f k8s/
```

2. Verify deployment:

```bash
kubectl get pods
kubectl get services
```

3. Access service:

```bash
minikube service healthservice-service
```

---

## Summary

* Automated CI with security and quality gates
* Secure Docker image publishing
* Kubernetes-based deployment
* Secrets handled securely
* Production-style DevSecOps workflow

---

