curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

docker build -t studentapp -f Dockerfile . 
docker run -d -p 5000:8080 --name studentapp_container studentapp


docker build -t testacrk8s.azurecr.io/dotnet-student-app:latest

docker tag 25f61e9f038a testacrk8s.azurecr.io/dotnet-student-app:latest

docker push testacrk8s.azurecr.io/dotnet-student-app:latest


kubectl create ns studentapp-dev

kubectl create secret docker-registry acr-secret \
  --docker-server=testacrk8s.azurecr.io \
  --docker-username=testacrk8s \
  --docker-password=<password> \
  --docker-email=balavenkatatejaatla@gmail.com \
   --namespace=studentapp-dev

https://learn.microsoft.com/en-us/azure/aks/ingress-basic?tabs=azure-cli

NAMESPACE=ingress-basic

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --create-namespace \
  --namespace $NAMESPACE \
  --set controller.replicaCount=2 \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true


kubectl get svc -n ingress-basic


helm create studentapp

studentapp/
│
├── charts/
│   └── (dependencies, if any)
│
├── templates/
│   ├── namespace.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── pod.yaml
│   ├── replicaset.yaml
│   ├── ingress.yaml
│   └── (other templates)
│
├── values.yaml
├── values-dev.yaml
├── values-staging.yaml
└── values-prod.yaml


helm upgrade --install studentapp ./studentapp --namespace studentapp-dev -f studentapp/values-dev.yaml

or 

helm upgrade --install studentapp ./studentapp  -f studentapp/values-dev.yaml

helm uninstall studentapp --namespace studentapp-dev


helm upgrade --install studentapp ./helm-studentapp -f helm-studentapp/values-dev.yaml \
  --set image.repository=testacrk8s.azurecr.io/dotnet-student-app \
  --set image.tag=v1



helm upgrade --install studentapp ./helm-studentapp  -f helm-studentapp/values-dev.yaml --set image.repository=testacrk8s.azurecr.io/dotnet-student-app --set image.tag=v1 --kubeconfig ~/.kube/config




my-ansible-project/
├── playbook.yml
└── roles/
    └── helm_deploy/
        ├── tasks/
        │   └── main.yml
        ├── defaults/
        │   └── main.yml
        └── vars/
            └── main.yml




ansible-playbook playbook.yml -e "helm_chart_path=../helm-studentapp/ release_name=studentapp namespace=studentapp-dev image_repository=testacrk8s.azurecr.io/dotnet-student-app image_tag=v1 kubeconfig_path=~/.kube/config"



docker system prune -a

