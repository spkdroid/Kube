## **Goal**
Deploy a basic web application using Kubernetes. The app will display a "Hello, Kubernetes!" message.

---

## **Prerequisites**
- Basic knowledge of Docker and containers.
- Kubernetes CLI (`kubectl`) installed.
- A Kubernetes cluster set up (Minikube or Docker Desktop recommended for local testing).

---

## **Step 1: Install `kubectl` and Minikube**

---

### **For Windows Users**

#### **1. Install `kubectl`**

1. **Download `kubectl` for Windows:**
   - Go to [kubectl releases](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/).
   - Download `kubectl.exe` and move it to a directory in your `PATH` (e.g., `C:\kubectl\`).

2. **Add `kubectl` to `PATH`:**
   - Open System Properties.
   - Go to **Advanced System Settings** > **Environment Variables**.
   - Select `Path` and click **Edit**.
   - Add the path to the directory where `kubectl.exe` is located.

3. **Verify installation:**
```powershell
kubectl version --client
```

---

####  **2. Install Minikube**

1. **Download Minikube:**
   - Go to [Minikube releases](https://minikube.sigs.k8s.io/docs/start/).
   - Download `minikube-windows-amd64.exe` and rename it to `minikube.exe`.

2. **Move `minikube.exe` to a directory in `PATH`:**
   - Move the file to a folder such as `C:\minikube\` and add that folder to your system `PATH`.

3. **Start Minikube:**
```powershell
minikube start
```

4. **Verify installation:**
```powershell
minikube version
kubectl cluster-info
```

---

###  **For macOS Users**

####  **1. Install `kubectl`**
```bash
brew install kubectl
```

####  **2. Install Minikube**
```bash
brew install minikube
```

####  **Start Minikube:**
```bash
minikube start
```

---

### **For Linux Users**

####  **1. Install `kubectl`**
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```

####  **2. Install Minikube**
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
minikube start
```

---

##  **Step 2: Create a Docker Container**

### 2.1 **Create a Simple Node.js App**
```bash
mkdir k8s-tutorial
cd k8s-tutorial
```

### 2.2 Create `app.js`
```javascript
// app.js
const http = require('http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/html');
  res.end('<h1>Hello, Kubernetes!</h1>');
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});
```

### 2.3 Create `Dockerfile`
```Dockerfile
# Use Node.js as base image
FROM node:18

# Create app directory
WORKDIR /usr/src/app

# Copy application files
COPY app.js .

# Start the app
CMD ["node", "app.js"]
```

---

## **Step 3: Build and Push Docker Image**

### 3.1 Build Docker image
```bash
docker build -t your-dockerhub-username/k8s-tutorial .
```

### 3.2 Log in to Docker Hub
```bash
docker login
```

### 3.3 Push the image to Docker Hub
```bash
docker push your-dockerhub-username/k8s-tutorial
```

---

## **Step 4: Create Kubernetes Configuration Files**

### 4.1 Create `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: k8s-tutorial
  labels:
    app: k8s-tutorial
spec:
  replicas: 2
  selector:
    matchLabels:
      app: k8s-tutorial
  template:
    metadata:
      labels:
        app: k8s-tutorial
    spec:
      containers:
      - name: k8s-tutorial
        image: your-dockerhub-username/k8s-tutorial
        ports:
        - containerPort: 3000
```

### 4.2 Create `service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: k8s-tutorial-service
spec:
  selector:
    app: k8s-tutorial
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer
```

---

##  **Step 5: Deploy to Kubernetes**

### 5.1 Start Minikube (if not running)
```bash
minikube start
```

### 5.2 Deploy the application
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### 5.3 Verify the pods are running
```bash
kubectl get pods
```

### 5.4 Check the service status
```bash
kubectl get svc
```

---

##  **Step 6: Access the Application**

###  **If using Minikube**
```bash
minikube service k8s-tutorial-service
```

###  **If using Docker Desktop or a cloud cluster**
- Get the external IP from:
```bash
kubectl get svc
```
- Open the browser at:
```
http://<EXTERNAL-IP>:80
```

---

##  **Step 7: Clean Up**

### Delete the deployment and service
```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

### Stop Minikube
```bash
minikube stop
```

---

## 📖 **Bonus: Useful Kubernetes Commands**

- Check cluster information:
```bash
kubectl cluster-info
```

- Get all running pods:
```bash
kubectl get pods
```

- Get details about a pod:
```bash
kubectl describe pod <pod-name>
```

- View application logs:
```bash
kubectl logs <pod-name>
```

---
