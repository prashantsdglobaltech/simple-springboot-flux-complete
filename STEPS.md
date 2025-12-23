
# STEP-BY-STEP SETUP GUIDE (BEGINNER)

## STEP 1: Install basic tools
sudo apt update
sudo apt install -y curl git unzip

## STEP 2: Install Docker
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
newgrp docker

## STEP 3: Install k3s
curl -sfL https://get.k3s.io | sh -

Configure kubectl:
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER:$USER ~/.kube/config

kubectl get nodes

## STEP 4: Install Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

## STEP 5: Install FluxCD
curl -s https://fluxcd.io/install.sh | sudo bash

Bootstrap Flux (replace values):
flux bootstrap github \
  --owner=YOUR_GITHUB \
  --repository=simple-springboot-flux-complete \
  --branch=main \
  --path=gitops/clusters/dev \
  --personal

## STEP 6: Build & push Spring Boot image
cd app
mvn clean package
docker build -t yourdockerhub/springboot:latest .
docker push yourdockerhub/springboot:latest

## STEP 7: Verify deployment
kubectl get pods
kubectl port-forward svc/springboot 8080:80

Test:
curl http://localhost:8080/hello
