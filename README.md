🖥️ EC2-1: Jenkins Server

Purpose:

Pull code from GitHub

Build Docker image

Push image to Docker Hub

Trigger Kubernetes deployment

Software installed:

Jenkins

Docker

Git

kubectl

Docker Hub login credentials

🖥️ EC2-2: Kubernetes Master Node

Purpose:

Control plane

API server

Responsible for deployments & scheduling pods

Software installed:

Kubernetes (kubeadm, kubelet, kubectl)

Docker or containerd

🖥️ EC2-3: Kubernetes Worker Node

Purpose:

Runs your Docker containers ("Pods")

Receives instructions from master

Software installed:

Kubernetes (kubeadm, kubelet)

Docker or containerd

✔ (1) GitHub Webhook

GitHub will automatically notify Jenkins when code is pushed.

Steps:

Go to GitHub repo → Settings

Webhooks → Add webhook

URL:

http://<EC2-Jenkins-IP>:8080/github-webhook/


Content Type: application/json

Trigger: Push Events

This makes pipeline run automatically.

✅ 📌 3. How Jenkins connects to Docker

Jenkins uses Docker CLI installed on the same machine.

Jenkins runs shell commands:
docker build -t myimage:latest .
docker login
docker push myimage:latest


So Jenkins → Docker (same machine).

For this:

sudo usermod -aG docker jenkins
sudo systemctl restart jenkins


This allows Jenkins to run Docker commands.

✅ 📌 4. How Jenkins connects to Kubernetes

Jenkins must have:

kubectl installed

kubeconfig file copied from Kubernetes master node

Steps:

Step 1: On Kubernetes Master Node:
cat ~/.kube/config


Copy the output.

Step 2: On Jenkins server:

Create file:

mkdir ~/.kube
nano ~/.kube/config


Paste content.

Now Jenkins can run:

kubectl apply -f k8s-deployment.yaml


This deploys the app on Kubernetes.

✅ 📌 5. How Kubernetes connects to Docker images

Kubernetes pulls image from Docker Hub:

Example YAML:

containers:
  - name: student-app
    image: yourdockerhub/student-app:latest


When you apply:

kubectl apply -f deployment.yaml


The Kubernetes worker node will:

Check Docker Hub

Pull the image

Run a pod containing your container
