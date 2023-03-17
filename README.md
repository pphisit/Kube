# ขั้นตอนการทำงาน Kube 

## ติดตั้ง kubectl binary with curl on macOS
1. Download the latest release:
    ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"

    ```
2. Test to ensure the version you installed is up-to-date:
    ```
    kubectl version --client

    ```
## ติดตั้ง minikube ผ่าน docker

1.
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-arm64

sudo install minikube-darwin-arm64 /usr/local/bin/minikube
```

2. Start your cluster 
```
minikube start --driver=docker

```
3. Interact with your cluster

```
kubectl get po -A
```
4. เปิดหน้า dashboard
```
minikube dashboard
```


