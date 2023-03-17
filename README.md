# ขั้นตอนการทำงาน Kube 

## ติดตั้ง kubectl on macOS
1. ดาวน์โหลด kubectl
    ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"

    ```
2. ทดสอบการติดตั้ง
    ```
    kubectl version --client

    ```
![](https://user-images.githubusercontent.com/109591322/225971271-f77c14fb-e9e4-4056-bc0b-3abf4b9e03d9.png)    
## ติดตั้ง minikube 

```
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-arm64

    sudo install minikube-darwin-arm64 /usr/local/bin/minikube
```

## ติดตั้ง Docker Desktop
* https://www.docker.com/products/docker-desktop/
## เริ่มต้นการใช้งานคลัสเตอร์
1. เริ่มใช้งานคลัสเตอร์ 
```
minikube start --driver=docker
```
2. Interact with your cluster
* สำหรับตรวจสอบว่า มีการใช้งานครบทุกตัวหรือป่าว
```
kubectl get po -A
```
![](https://user-images.githubusercontent.com/109591322/225971318-641ee0df-74ab-47f3-ad5f-7fba57cb0342.png)
3. เปิดหน้า dashboard
```
minikube dashboard
```

![](https://user-images.githubusercontent.com/109591322/225972454-a088793f-a692-4c9b-8146-e82e5cac4886.png)
4. ตั้ง LoadBalancer
```
minikube tunnel
```
## ติดตั้ง Traefik
1. ติดตั้งการกำหนดการใช้งานทรัพกรของ Traefik 
```
kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-definition-v1.yml
```

2. ติดตั้ง RBAC สำหรับ Traefik  
```
kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-rbac.yml
```

3. ติดตั้ง Traefik Helmchart  
```
helm repo add traefik https://traefik.github.io/charts
helm repo update
helm install traefik traefik/traefik
```
4. ตรวจสอบว่าบริการกำลังทำงานอยู่

```
kubectl get svc -l app.kubernetes.io/name=traefik
kubectl get po -l app.kubernetes.io/name=traefik
```

### create secrete
* สร้างไฟล์ auth-secret และกำหนด username และ password ในการเข้า Traefik

```
 htpasswd -nB user | tee auth-secret
```


### Dry run to create a secret deployment.
* สร้างไฟล์ dashboard-secret.yaml
```
kubectl create secret generic -n traefik dashboard-auth-secret \
--from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
```
#### กำหนด namespace
```
./traefik-setup.sh
```

* นำ namespace ที่ตั้งใส่ในไฟล์ traefik-dashboard.yaml,rancher-deployment.yaml,dashboard-secret.yaml
#### ตั้ง Hosts File
```
sudo nano /etc/hosts
```
```
127.0.0.1       web.spcnXX.local
127.0.0.1       traefik.spcnXX.local
```
### Deploy
```
kubectl apply -f .
```
## ผลลัพธ์
![](https://user-images.githubusercontent.com/109591322/225971304-e839ff2d-4365-4b26-941e-77cb27adf779.png)
![](https://user-images.githubusercontent.com/109591322/225971314-a714277c-041b-492f-82e6-b28101954822.png)
![](https://user-images.githubusercontent.com/109591322/225971295-f5ba9784-8f6c-4523-b266-6bb55f4a74c8.png)