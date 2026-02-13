# 🚀 Minikube Setup on Google Cloud Platform (GCP)

This guide walks you through setting up **Minikube** on a **Google Cloud Platform (GCP)** virtual machine. You'll create a VM, install Docker and kubectl, run Minikube using the Docker driver, deploy a sample Kubernetes application, and expose it externally via GCP firewall rules.

This setup provides a complete hands‑on experience in:

✔ Kubernetes basics  
✔ Deployment & service exposure  
✔ Port forwarding  
✔ Using GCP firewall rules  
✔ Accessing Minikube workloads externally

***

## 📌 **1. Introduction**

Setting up Minikube on a GCP VM is an excellent way to learn Kubernetes in a controlled yet cloud‑based environment. You will deploy a lightweight local Kubernetes cluster inside a VM and access it from your external browser.

In this guide you will:

*   Create a VM using GCP Shell
*   Install Docker
*   Install kubectl
*   Install and start Minikube
*   Deploy a sample application
*   Access it via the VM’s external IP

***

## 🛠 **2. Create VM Using GCP Shell**

### Check allowed machine types:

```bash
gcloud compute machine-types list --zones=us-central1-a
```

### Create VM:

```bash
gcloud compute instances create minikube-vm \
  --zone=us-central1-a \
  --machine-type=e2-medium \
  --image-family=ubuntu-2204-lts \
  --image-project=ubuntu-os-cloud \
  --boot-disk-size=30GB
```

***

## 🐳 **3. Install Docker Inside VM**

SSH into your VM → *minikube-vm*  

![SS-into-VM](https://github.com/Hemanshu2003/kubernetes-on-gcp-minikube-docker-driver/blob/main/assets/1.png)

Run:

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

***

## ⎈ **4. Install kubectl (Linux x86‑64)**

### Download latest binary:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

### (Optional) Validate checksum:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

Expected output (if valid):

    kubectl: OK

### Install kubectl:

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

If non-root installation:

```bash
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```

### Verify installation:

```bash
kubectl version --client
```

🔗 More info: <https://kubernetes.io/docs>

***

## 🌱 **5. Install Minikube**

Download Minikube binary:

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

### Start Minikube (IMPORTANT: Use Docker driver)

```bash
minikube start --driver=docker
```

***

## 🚀 **6. Deploy a Sample Application**

### Create deployment:

```bash
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
```

### Expose deployment using NodePort:

```bash
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

### Check service:

```bash
kubectl get services hello-minikube
```

### Access via Minikube’s built-in method:

```bash
minikube service hello-minikube
```

### (Optional) Port forward:

```bash
kubectl port-forward service/hello-minikube 7080:8080
```

🔗 Minikube docs: <https://minikube.sigs.k8s.io/docs>

***

## 🌐 **7. Access Your App Externally (GCP External IP)**

### Step 7.1 — Inside VM:

```bash
kubectl port-forward svc/hello-minikube 8080:8080 --address=0.0.0.0
```

Keep this running.

***

### Step 7.2 — Create Firewall Rule in GCP Shell:

```bash
gcloud compute firewall-rules create allow-8080 \
  --allow tcp:8080
```

***

### Step 7.3 — Access from browser:

Find EXTERNAL\_IP from GCP → VM Instances. Copy the External IP

![SS-into-VM](https://github.com/Hemanshu2003/kubernetes-on-gcp-minikube-docker-driver/blob/main/assets/2.png)

Open:

    http://EXTERNAL_IP:8080

🎉 Your application is now live and accessible!

![SS-into-VM](https://github.com/Hemanshu2003/kubernetes-on-gcp-minikube-docker-driver/blob/main/assets/3.png)

***

## 🏁 **Conclusion**

You now have a fully functional Minikube Kubernetes cluster running inside a GCP VM. You:

*   Provisioned a VM
*   Installed Docker, kubectl, and Minikube
*   Started a Kubernetes cluster
*   Deployed & exposed a sample app
*   Opened a firewall rule to access it externally

This environment is perfect for learning, experimenting, and testing Kubernetes workloads without using a full GKE cluster.

***

## ✨ Author

**Hemanshu Anil Waghmare (CG)**  
Feel free to reach out on **[LinkedIn](http://www.linkedin.com/in/hemanshu-anil-waghmare-50a7a3291)**!

***
