Objective: The take home assignment is to build a local cluster using minikube, docker desktop or kind, and host a simple hello world app, and add in cluster monitoring for the app. You should check in the code and any documentation to a git repo for review and send us the link to the repo.

Steps:

## 1. Fire the below command to install the minikube and start the local Kubernetes cluster.

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube && minikube start

## 2. Use the HelloWorld helm chart to run the application.

helm install myworld helloworld

## 3. Get the Nodeport Service URL by using the below command and paste the URL to any browser, it will show the HelloWorld page.

minikube service myworld-helloworld --url

#########################################################################################################################################

## 4. To deploy Prometheus using Helm Please follow the Below instructions.

# Create a monitoring namespace.

kubectl create namespace monitoring

# Add the prometheus-community chart repository.

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Deploy Prometheus.

helm upgrade -i prometheus prometheus-community/prometheus --namespace monitoring

# Run the Below command to enable the port forwarding.

export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace monitoring port-forward $POD_NAME 9090

# Please the below URL to access the prometheus dashboard.

http://127.0.0.1:9090
