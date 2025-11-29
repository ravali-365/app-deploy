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


<img width="1117" height="727" alt="Screenshot 2025-11-29 at 00 40 30" src="https://github.com/user-attachments/assets/70fbe375-d218-4c5c-b448-4402ac8af410" />

<img width="757" height="489" alt="Screenshot 2025-11-29 at 00 35 29" src="https://github.com/user-attachments/assets/210757b4-6e5a-4031-ba26-afb1ee55f287" />

<img width="588" height="655" alt="Screenshot 2025-11-29 at 00 36 32" src="https://github.com/user-attachments/assets/aa5fb407-029a-4f13-b1fb-ac622edcac56" />



Conclusion:
This project showcases end-to-end DevOps automation using GitOps methodology — a complete working example of how modern cloud-native teams achieve continuous delivery with Kubernetes and Argo CD.

Author: Ravali Billakanti

