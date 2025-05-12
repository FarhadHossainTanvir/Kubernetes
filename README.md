# Kubernetes: Auto Scaling and Monitoring (HPA + Prometheus + Grafana)

This guide documents how to set up and test **auto scaling** with Horizontal Pod Autoscaler (HPA) and **monitoring** with Prometheus & Grafana in a local Kubernetes cluster using Minikube.

---

## 🚀 Prerequisites

- Minikube installed and running
- kubectl installed
- Helm installed

Start Minikube and enable metrics server:

```bash
minikube start
minikube addons enable metrics-server


# Step 1: Create a CPU-Intensive Deployment

Create a file called cpu-app.yaml:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpu-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cpu-app
  template:
    metadata:
      labels:
        app: cpu-app
    spec:
      containers:
      - name: cpu-stress
        image: vish/stress
        args:
        - -cpus
        - "1"
        resources:
          requests:
            cpu: "100m"
          limits:
            cpu: "200m"

Apply it:

kubectl apply -f cpu-app.yaml

Expose the deployment (if not done yet):

kubectl expose deployment cpu-app --port=80 --target-port=80

🔄 Step 2: Set Up Horizontal Pod Autoscaler (HPA)

kubectl autoscale deployment cpu-app --cpu-percent=50 --min=1 --max=5

Check HPA status:

kubectl get hpa

🔁 Step 3: Simulate CPU Load

Start a BusyBox pod:

kubectl run -i --tty load-generator --image=busybox /bin/sh

Inside the container, run this loop:

while true; do wget -q -O- http://cpu-app; done

Watch pods scaling:

kubectl get pods -w
kubectl get hpa -w

📊 Step 4: Monitoring with Prometheus and Grafana
Add Prometheus Helm repo:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

Install Prometheus stack:

helm install prometheus prometheus-community/kube-prometheus-stack

Port-forward to access Grafana:

kubectl port-forward svc/prometheus-grafana 3000:80

Open in browser: http://localhost:3000

    Username: admin

    Password: prom-operator

Port-forward Prometheus (optional):

kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090

Access: http://localhost:9090
🧹 Cleanup (Optional)

kubectl delete deployment cpu-app
kubectl delete hpa cpu-app
kubectl delete pod load-generator
helm uninstall prometheus

✅ Summary
Task	Tool / Command
Deploy stress app	kubectl apply -f cpu-app.yaml
Enable metrics	minikube addons enable metrics-server
Create HPA	kubectl autoscale deployment ...
Generate load	Busybox with wget loop
Install monitoring stack	helm install prometheus ...
View dashboards	Grafana → http://localhost:3000
