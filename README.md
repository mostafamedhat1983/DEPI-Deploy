# Automated CI/CD Pipeline: Docker Image Build and Deployment on EKS with Amazon Aurora MySQL and Load Balancer , HPA Integration. Monitoring using Prometheus and Grafana.

![image](https://github.com/user-attachments/assets/b5a95a90-66f7-4cac-a36f-79d7ad2a1096)
![image](https://github.com/user-attachments/assets/e2d6261e-1e31-492a-bd22-450c2cb5645a)
![image](https://github.com/user-attachments/assets/384a318d-6d41-408f-b7a4-2c1f9c65582c)
![image](https://github.com/user-attachments/assets/7b264553-a79f-48fd-853f-8d5711ea450d)
![image](https://github.com/user-attachments/assets/54e80bb6-18a4-45ad-a0c6-5b3ab5e89ef6)
![image](https://github.com/user-attachments/assets/981a68a4-6e87-4866-88da-019bbe2ffaa6)
![image](https://github.com/user-attachments/assets/2c2afc6f-1ef3-4f5f-9f28-13097755b7d8)
![image](https://github.com/user-attachments/assets/a6d71634-c116-48e7-a747-7bf274b32776)
![image](https://github.com/user-attachments/assets/0258fc75-b5d7-443a-8086-16269013abbe)
![image](https://github.com/user-attachments/assets/eb2b7772-a4e3-41f1-a328-a09efacbad8a)
![image](https://github.com/user-attachments/assets/3267baa5-510b-489d-b3e3-0c6a50f2bc6d)

![deployment (2)](https://github.com/user-attachments/assets/81399f23-9828-46ad-b642-98ca16934d4c)



# 🚀 Jenkins CI/CD Pipeline Documentation

This pipeline automates the build, test, delivery, and deployment process for a Dockerized application using Jenkins, Docker Hub, AWS EKS and AWS Aurora Mysql.


## 🧱 Stage 1: Build & Test

- 🔨 Builds the Docker image from the `/app` directory.
- 🏷️ Tags the image using the current Git commit (`${GIT_COMMIT}`).

---

## 🚚 Stage 2: Delivery (Push to Docker Hub)

- 🔐 Uses Jenkins stored credentials (`Docker_Creds`).
- 🔑 Authenticates to Docker Hub.
- 📤 Pushes the built image with the unique Git commit tag.

---

## Stage 3: Deploy to AWS EKS with Helm

- 🔑 Uses AWS credentials stored in Jenkins (aws-cred)
- 🔑 Uses Docker Hub credentials for image pulling (docker-hub-cred)
- 🌐 Connects to the EKS cluster (python-app-cluster) via AWS CLI
- 📦 Deploys the application using Helm chart instead of individual YAML files
- 🔄 Handles environment variables and credentials through Helm values

---

## 📊 Stage 4: Deploy Metrics Server & HPA

- 🔑 Uses AWS credentials stored in Jenkins (`aws-cred`)
- 🛠️ Applies configuration from `metrics-server.yaml`
- ⚖️ Configures Horizontal Pod Autoscaler (HPA)
   - 🎯 Target CPU utilization: 75%
   - 🔽 Minimum pods: 2
   - 🔼 Maximum pods: 5
   - 🔄 Automatically scales based on CPU usage
- 📈 Enables resource monitoring and auto-scaling
- 🚀 Optimizes performance under varying workloads

---

## 📊 Stage 5: Deploy Prometheus and Grafana

🔑 **Uses AWS Credentials**: Utilizes AWS credentials stored in Jenkins (`aws-cred`).
📥 **Add Helm Repository**: Adds the Prometheus community Helm charts repository.
🚀 **Install Prometheus**: Deploys Prometheus using the `kube-prometheus-stack` chart.
- 🗂️ **Namespace**: Deploys in the `monitoring` namespace.
- 📦 **Storage Class**: Sets `gp2` for persistent volumes.
📄 **Apply Grafana Configuration**: Uses `kubectl` to apply Grafana load balancer configuration from `grafana-lb.yaml`.
📊 **Enables Monitoring**: Sets up Prometheus and Grafana for monitoring Kubernetes clusters.

---

## 📊 Summary Table

| Stage                      | Description                                           | Tools Involved                                                                 |
|----------------------------|-------------------------------------------------------|--------------------------------------------------------------------------------|
| 🧱 Build & Test            | Build Docker image from codebase                      | Docker                                                                         |
| 🚚 Delivery                | Push Docker image to Docker Hub                       | Docker, Jenkins credentials: Docker_Creds🐳                                     |
| ☁️ Deploy to EKS           | Deploy app and services to AWS EKS using Helm chart                   | AWS CLI, kubectl, Helm, Kubernetes, Jenkins credentials: aws-cred ☁️,db_endpoint🌐,DB_HOST 🏠, DB_USER 👤, DB_PASS 🔑,  DB_DATABASE 🗄️       |
| ⚖️ Deploy Metrics Server   | Configure HPA and deploy Metrics Server               | kubectl, Metrics Server, HPA, Jenkins credentials: aws-cred 📊                 |
| 📊 Deploy Prometheus and Grafana | Set up monitoring with Prometheus and Grafana        | Helm, kubectl, Jenkins credentials: aws-cred 🔑                                 |


---

> ✅ **Tip:** Make sure your credentials (`Docker_Creds`, `db_endpoint`, `aws-cred`,`DB_HOST`,`DB_USER`,`DB_PASS`,`DB_DATABASE`) are set up in Jenkins before running this pipeline. Also install these 2 jenkins plugins: AWS Credentials and Pipeline: AWS Steps 



