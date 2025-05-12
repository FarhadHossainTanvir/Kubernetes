# 🚀 Kubernetes: Auto Scaling and Monitoring (HPA + Prometheus + Grafana)

This guide walks through how to set up and test **auto scaling** with Horizontal Pod Autoscaler (HPA) and **monitoring** using Prometheus & Grafana in a local Kubernetes cluster powered by Minikube.

---

## 📋 Prerequisites

- ✅ Minikube installed and running
- ✅ `kubectl` CLI installed
- ✅ Helm 3 installed

Start your Minikube cluster and enable the metrics server:

```bash
minikube start
minikube addons enable metrics-server
