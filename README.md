# Example-Voting-App-with-HPA-on-AKS


This repository demonstrates deploying the **[Example Voting App](https://github.com/dockersamples/example-voting-app)** to **Azure Kubernetes Service (AKS)** with **Horizontal Pod Autoscaling (HPA)**. The cluster resources and scaling behavior are visualized using **Lens Kubernetes IDE**.

---

## 📌 Repository Name

`example-voting-app-hpa-aks`

---

## 🚀 Features

* Deploys the **Example Voting App** on AKS.
* Configures **CPU/Memory resource requests and limits** for workloads.
* Applies **Horizontal Pod Autoscaler (HPA)** to scale the `vote` Deployment.
* Uses **Azure Monitor Metrics Server** to collect CPU usage.
* Visualizes scaling events and resource usage in **Lens**.

---

## 🛠️ Prerequisites

* Azure Subscription (Free Trial/Student works fine).
* [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli).
* [kubectl](https://kubernetes.io/docs/tasks/tools/).
* [Helm](https://helm.sh/docs/intro/install/).
* [Lens IDE](https://k8slens.dev/).

---

## ⚡ Setup Steps

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/example-voting-app-hpa-aks.git
cd example-voting-app-hpa-aks
```

### 2. Create AKS Cluster

```bash
az group create --name myResourceGroup --location eastus
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 2 --generate-ssh-keys
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

### 3. Deploy Metrics Server (Required for HPA)

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### 4. Deploy Application

```bash
kubectl apply -f k8s-specifications/
```

### 5. Apply HPA for Vote Deployment

```bash
kubectl apply -f k8s-specifications/hpa-vote.yaml
```

Example `hpa-vote.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: vote-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: vote
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

### 6. Stress Test the Application (Optional)

```bash
kubectl run -it load-generator --rm --image=busybox -- sh
while true; do wget -q -O- http://vote; done
```

### 7. Visualize in Lens

1. Open Lens.
2. Connect to your AKS cluster (`~/.kube/config`).
3. View **Workloads → Deployments** and **Autoscaling → HPA**.
4. Observe scaling behavior under load.
![WhatsApp Image 2025-09-05 at 14 11 22_87d9eda8](https://github.com/user-attachments/assets/88bd27eb-ecbf-4329-849d-3b88edfa2449)

---

## 📊 Expected Output

* `vote` Deployment scales between **1–5 replicas** based on CPU utilization.
* HPA events visible in Lens.
* Metrics graphs showing pod scaling activity.

---

## 📚 References

* [Kubernetes HPA Docs](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
* [Lens IDE](https://k8slens.dev/)
* [Azure AKS Docs](https://learn.microsoft.com/en-us/azure/aks/)

---

✅ With this setup, you’ll see **real-time pod autoscaling** inside Lens while running the Example Voting App on AKS.

---

