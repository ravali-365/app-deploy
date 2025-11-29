GitOps-Based Kubernetes Deployment using Argo CD

Overview: This project demonstrates a GitOps-based continuous delivery workflow using Argo CD and Kubernetes.  
It automates application deployment by continuously synchronizing Kubernetes manifests stored in a GitHub repository with the live Kubernetes cluster — eliminating manual `kubectl` operations.


Tech Stack:
- Kubernetes (Minikube) – Local test cluster  
- Argo CD – GitOps continuous delivery tool  
- GitHub – Version-controlled manifest source  
- Nginx – Sample web application  
- Helm (optional) – Template and packaging (for future expansion)


Setup & Deployment Steps:

1) Start Kubernetes Cluster
minikube start --driver=docker --memory=4096 --cpus=2

2) Install Argo CD:
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl get pods -n argocd

3) Access Argo CD UI:
kubectl port-forward svc/argocd-server -n argocd 8080:443

Get the initial admin password:
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo

Then login:
argocd login localhost:8080 --username admin --password <generated password> --insecure

4) Connect GitHub Repository:
argocd repo add https://github.com/ravali-365/app-deploy.git

5) Create Argo CD Application:
Apply the following definition:
kubectl apply -f app.yaml

6) Verify Deployment:
kubectl get pods
kubectl get svc
minikube service nginx-service
┌───────────┬───────────────┬─────────────┬───────────────────────────┐
│ NAMESPACE │     NAME      │ TARGET PORT │            URL            │
├───────────┼───────────────┼─────────────┼───────────────────────────┤
│ default   │ nginx-service │ 80          │ http://192.168.49.2:31338 │
└───────────┴───────────────┴─────────────┴───────────────────────────┘
🏃  Starting tunnel for service nginx-service./┌───────────┬───────────────┬─────────────┬────────────────────────┐
│ NAMESPACE │     NAME      │ TARGET PORT │          URL           │
├───────────┼───────────────┼─────────────┼────────────────────────┤
│ default   │ nginx-service │             │ http://127.0.0.1:58345 │
└───────────┴───────────────┴─────────────┴────────────────────────┘
🏃  Starting tunnel for service nginx-service.
🎉  Opening service default/nginx-service in default browser...
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.

Repository Structure:
.
├── deployment.yaml       # Nginx Deployment manifest
├── service.yaml          # NodePort Service exposing Nginx
└── app.yaml              # Argo CD Application definition

Architechture Diagram:
┌────────────────────────────┐
│        GitHub Repo         │
│ (deployment.yaml, service) │
└─────────────┬──────────────┘
              │
              ▼
      ┌────────────────┐
      │     Argo CD     │
      │  (GitOps Engine)│
      └───────┬────────┘
              │
              ▼
   ┌──────────────────────┐
   │ Kubernetes Cluster    │
   │ (Nginx Deployment)    │
   └──────────────────────┘
              │
              ▼
     🌐 Accessible via NodePort

<img width="588" he<img width="757" height="489" alt="Screenshot 2025-11-29 at 00 35 29" src="https://github.com/user-attachments/assets/0d2d0a1e-61c3-445b-a5ea-2fa71e1af592" />
ight="655" alt="Screenshot 2025-11<img width="649" height="486" alt="Screenshot 2025-11-29 at 00 38 49" src="https://github.com/user-attachments/assets/1ec43b09-d9cd-4ed1-b000-83a9d4b022a7" />
-29 at 00 36 32" src="https://github.com/user-attachments/assets/c1c4774e-ff75-4003-9397-cd86e2dc17a8" />

     <img width="1117" height="727" alt="Screenshot 2025-11-29 at 00 40 30" src="https://github.com/user-attachments/assets/38dca53c-9d19-44d7-88d2-5def9c6559ff" />
