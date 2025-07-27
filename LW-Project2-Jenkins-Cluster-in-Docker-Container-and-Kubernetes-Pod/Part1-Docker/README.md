# Jenkins Master-Slave CI/CD Pipeline in Docker 🐳

---

## 📝 **Project Overview**

This project demonstrates a complete **Jenkins Master-Slave (Agent) setup inside Docker containers** along with a fully functional **CI/CD pipeline**.

The pipeline automatically:

* Pulls source code from GitHub
* Builds Docker images inside Jenkins Slave
* Runs Docker containers
* Performs health checks post-deployment

This setup closely simulates a real-world Jenkins distributed build architecture.

---

##  **App Source Code Repo**
https://github.com/Sayantan2k24/flask-app-for-jenkins-docker-example.git

## 📌 **Tech Stack Used**

* **Jenkins (Master + Slave)**
* **Docker (Docker-in-Docker for nested Docker builds)**
* **GitHub (Source Code Repository)**
* **SSH (for Master-Slave communication)**
* **Shell scripting (for build and deployment)**

---

## 🔧 **Architecture**

* Jenkins Master runs inside a Docker container.
* Jenkins Slave (Agent) runs inside another Docker container.
* Master and Slave communicate securely via SSH.
* Both containers run Docker inside (using `--privileged` mode) to allow Docker operations from inside Jenkins jobs.
* The complete build and deployment happen inside the Jenkins Slave node.

---

## 🏗 **Project Setup**

### 1️⃣ **Build Jenkins Master Docker Image**

Prepare your `Dockerfile-master` with necessary Jenkins and Docker installation.

```bash
docker build -t jenkins-master:rhel9 -f Dockerfile-master .
docker run --privileged -d --name jenkins-master -p 8080:8080 jenkins-master:rhel9
```

> Access Jenkins UI:
> `http://<VM-IP>:8080`

---

### 2️⃣ **Build Jenkins Slave Docker Image**

Prepare your `Dockerfile-slave` with necessary Docker and SSH server installation.

```bash
docker build -t jenkins-slave:rhel9 -f Dockerfile-slave .
docker run --privileged -d --name jenkins-slave jenkins-slave:rhel9
```

---

### 3️⃣ **Configure SSH Communication**

* Generate SSH keys inside Jenkins Master:

```bash
docker exec -it jenkins-master ssh-keygen
```

* Copy public key to Jenkins Slave:

```bash
docker cp jenkins-master:/var/lib/jenkins/.ssh/id_rsa.pub id_rsa.pub
docker cp id_rsa.pub jenkins-slave:/home/jenkins/.ssh/authorized_keys
```

* Verify SSH access from Master to Slave.

---

### 4️⃣ **Add Jenkins Slave Node**

* Go to **Manage Jenkins > Nodes > New Node** in Jenkins UI.
* Remote root directory: `/home/jenkins` (home directory of `jenkins` user inside slave).
* Executors: `1`
* Label: e.g. `agent-node`
* Launch Method: **SSH**
* Provide SSH credentials (username + private key).

---

### 5️⃣ **Test Freestyle Job**

* Create a Freestyle job.
* Assign it to the agent node using the configured label.
* Add simple shell build steps and verify execution happens on the slave.

---

### 6️⃣ **Create Jenkins Pipeline**

Use the following Jenkinsfile for your declarative pipeline:

```groovy
pipeline {
    agent { label 'agent-node' } 
    environment {
        IMAGE_NAME = "sayantan2k21/demo-app"
        IMAGE_TAG = "v1.0-${BUILD_NUMBER}"
        CONTAINER_NAME = "demo-app-container"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Sayantan2k24/flask-app-for-jenkins-docker-example.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
        stage('Run Docker Container') {
            steps {
                sh """
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d -p 5000:5000 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
        stage('Health Check') {
            steps {
                sh "sleep 10 && curl localhost:5000"
            }
        }
    }
    post {
        always {
            echo "Job executed on agent: ${env.NODE_NAME}"
        }
    }
}
```

---

## 🚀 **Output Verification**

* Application deployed successfully inside Docker container on Slave node.
* Access Flask app via:

```bash
http://<slave-vm-ip>:5000
or
http://<host-ip>:<forwarded-port>
```

* Check Docker containers running inside the Slave:

```bash
docker ps
```

---

## 🎯 **Key Takeaways**

* ✅ Full Jenkins Master-Slave Setup using Docker
* ✅ Docker-in-Docker (DinD) configuration
* ✅ SSH Key-based secure communication
* ✅ End-to-End CI/CD Pipeline execution
* ✅ Real-world project simulation

---

## 🤝 **Connect & Collaborate**

If you found this project helpful or want to collaborate, feel free to connect!

**LinkedIn:** [Sayantan Samanta](https://www.linkedin.com/in/sayantan-samanta/)

---

