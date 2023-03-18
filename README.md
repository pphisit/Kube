# wakatime
* https://wakatime.com/@spcn29/projects/kyzcolykln
---
* [ติดตั้ง kubectl on macOS](https://github.com/pphisit/Kube#%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-kubectl-on-macos)

* [ติดตั้ง minikube](https://github.com/pphisit/Kube#%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-minikube)

* [ติดตั้ง Docker Desktop](https://github.com/pphisit/Kube#%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-docker-desktop)
* [เริ่มต้นการใช้งานคลัสเตอร์](https://github.com/pphisit/Kube#%E0%B9%80%E0%B8%A3%E0%B8%B4%E0%B9%88%E0%B8%A1%E0%B8%95%E0%B9%89%E0%B8%99%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B9%83%E0%B8%8A%E0%B9%89%E0%B8%87%E0%B8%B2%E0%B8%99%E0%B8%84%E0%B8%A5%E0%B8%B1%E0%B8%AA%E0%B9%80%E0%B8%95%E0%B8%AD%E0%B8%A3%E0%B9%8C)
* [ติดตั้ง Traefik](https://github.com/pphisit/Kube#%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-traefik)
* [สร้างไฟล์ .yaml](https://github.com/pphisit/Kube#%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87%E0%B9%84%E0%B8%9F%E0%B8%A5%E0%B9%8C-yaml)
* [Deploy](https://github.com/pphisit/Kube#deploy)
* [ผลลัพธ์](https://github.com/pphisit/Kube#%E0%B8%9C%E0%B8%A5%E0%B8%A5%E0%B8%B1%E0%B8%9E%E0%B8%98%E0%B9%8C)
---
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
![](https://user-images.githubusercontent.com/109591322/226112507-c1103905-112d-4502-91c9-f02ec5b0d0dc.png)
## ติดตั้ง Docker Desktop
* https://www.docker.com/products/docker-desktop/
## เริ่มต้นการใช้งานคลัสเตอร์
1. เริ่มใช้งานคลัสเตอร์ 
```
minikube start --driver=docker
```
![](https://user-images.githubusercontent.com/109591322/226112504-c74c5422-ee81-44e7-a1f5-c9e569fad1c6.png)
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


 Dry run to create a secret deployment.
* สร้างไฟล์ dashboard-secret.yaml
```
kubectl create secret generic -n traefik dashboard-auth-secret \
--from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
```
* คัดลอง user ที่อยู่ในไฟล์ dashboard-secret.yaml ใส่ใน user ของไฟล์ traefik-dashboard.yaml
#### กำหนด namespace
```
./traefik-setup.sh
```

* นำ namespace ที่ตั้งใส่ในไฟล์ traefik-dashboard.yaml,rancher-deployment.yaml,dashboard-secret.yaml

## สร้างไฟล์ .yaml
* traefik-dashboard.yaml

    <details>
    <summary>แสดงโค้ด</summary>
        

            apiVersion: traefik.containo.us/v1alpha1
            kind: Middleware
            metadata:
            name: traefik-basic-authen
            namespace: default
            spec:
            basicAuth:
                secret: dashboard-auth-secret
                removeHeader: true
            apiVersion: v1
            data:
            users: dXNlcjokMnkkMDUkQmZxLk9Kd25Nb05NRkxPYlA1NWNndXZFcHloQjZ6Smo2TWFua2lQYkJ3Qk52TE1haHpHdWEKCg==
            kind: Secret
            metadata:
            name: dashboard-auth-secret
            namespace: default

            apiVersion: traefik.containo.us/v1alpha1
            kind: IngressRoute
            metadata:
            name: traefik-dashboard
            namespace: default
            annotations:
                kubernetes.io/ingress.class: traefik
                traefik.ingress.kubernetes.io/router.middlewares: traefik-basic-authen
            spec:
            entryPoints:
                - websecure
            routes:
                - match: Host(`traefik.spcn29.local`) && (PathPrefix(`/dashboard`) || PathPrefix(`/api`))
                kind: Rule
                middlewares:
                    - name: traefik-basic-authen
                    namespace: default
                services:
                    - name: api@internal
                    kind: TraefikService     

    </details>

* rancher-deployment.yaml
    <details>
    <summary>แสดงโค้ด</summary>

        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: rancher-deployment
        namespace: default
        spec:
        replicas: 1
        selector:
            matchLabels:
            app: rancher 
        template:
            metadata:
            labels:
                app: rancher
            spec:
            containers:
            - name: rancher
                image: rancher/hello-world
                ports:
                - containerPort: 80
        
        apiVersion: v1
        kind: Service
        metadata:
        name: rancher-service
        labels:
            name: rancher-service
        namespace: default
        spec:
        selector:
            app: rancher
        ports:
        - name: http
            port: 80
            protocol: TCP
            targetPort: 80
        
        apiVersion: traefik.containo.us/v1alpha1
        kind: IngressRoute
        metadata:
        name: traefik-ingress
        namespace: default
        spec:
        entryPoints:
            - web
            - websecure
        routes:
        - match: Host(`web.spcn29.local`)
            kind: Rule
            services:
            - name: rancher-service
            port: 80
        
    </details>

#### ตั้ง Hosts File
* แสดง ip
```
kubectl get svc
```
![](https://user-images.githubusercontent.com/109591322/226112500-3049c959-4279-4186-89cb-17b44459dd9f.png)
* นำ ip ที่ได้มาใส่ได้ไฟล์ hosts 
* ตั้ง Hosts File
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

![](https://user-images.githubusercontent.com/109591322/226116974-21ee6074-4fb8-43ae-bb0b-1078db55f0a3.png)
![](https://user-images.githubusercontent.com/109591322/226116967-3b910bdd-1315-46c7-9111-91b83313ac8a.png)
![](https://user-images.githubusercontent.com/109591322/226116981-1068bcd6-1850-4a5d-95ec-f3e0835f2643.png)
![](https://user-images.githubusercontent.com/109591322/225971314-a714277c-041b-492f-82e6-b28101954822.png)