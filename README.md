**Objective:** The take home assignment is to build a local cluster using minikube, docker desktop or kind, and host a simple hello world app, and add in cluster monitoring for the app. You should check in the code and any documentation to a git repo for review and send us the link to the repo.

Steps:

# Deploy the Minikube.

### Fire the below command to install the minikube and start the local Kubernetes cluster.
```
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube && minikube start
```
# Deploy the HelloWorld Application.

### Use the HelloWorld helm chart to run the application. The nginx-Prometheus exporter is also included in the below chart to scrape the nginx metrics.
```
   helm install myworld helloworld
```
**Note: If you face the ImagePullBackOff error while deploying the template then please login your DockerHub account via Docker login command.
**

### Get the Nodeport Service URL by using the below command and paste the URL to any browser, it will show the HelloWorld page. It for the testing only otherwise it's highly recommended to integrate the application with the ingress load balancer.

```
   minikube service myworld-helloworld --url
```
![Alt text](screenshots/helloworld.jpg?raw=true "HelloWorld.jpg")

![Alt text](screenshots/nginx_metrics.jpg?raw=true "nginx_metrics.jpg")

# Deploy Prometheus using Helm Please follow the Below instructions.

### Create a monitoring namespace.
```
   kubectl create namespace monitoring
```
### Add the prometheus-community chart repository.
```
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
### Deploy Prometheus.
```
   helm upgrade -i prometheus prometheus-community/prometheus --namespace monitoring
```
### Run the Below command to enable the port forwarding.
```
   export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")

   kubectl --namespace monitoring port-forward $POD_NAME 9090
```
### Use the below URL to access the prometheus dashboard.
```
   http://127.0.0.1:9090
```
### Updated the scrape source to prometheus ConfigMap.

```
- job_name: nginx-exporter
  metrics_path: /metrics
  static_configs:
  - targets:
    - prometheus-server.monitoring.svc.cluster.local:8080
```

![Alt text](screenshots/Prometheus.jpg?raw=true "Prometheus.jpg")

# To deploy Grafana Please follow the Below instructions.

### Add the Grafana chart repository
```
   helm repo add grafana https://grafana.github.io/helm-charts
```
### Deploy Grafana Server.
```
   helm install grafana grafana/grafana -n monitoring
```
### Get your 'admin' user password by running:
```
   kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
### Get the Grafana URL to visit by running these commands in the same shell:
```
   export POD_NAME=$(kubectl get pods --namespace monitoring -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")
   kubectl --namespace monitoring port-forward $POD_NAME 3000
```
### Use the below URL to access the Grafana dashboard.
```
   http://127.0.0.1:3000
```   
![Alt text](screenshots/grafana.jpg?raw=true "grafana.jpg")

# Minikube node monitoring Dashboard.

![Alt text](screenshots/Minikube_1.jpg?raw=true "Minikube_1.jpg")

![Alt text](screenshots/Minikube_2.jpg?raw=true "Minikube_2.jpg")


# Application Monitoring Dashboard

![Alt text](screenshots/Application_1.jpg?raw=true "Application_1.jpg")


**In this task, we have touched the high level of monitoring of the application. Below are some recommended metrics which need to be monitored.**
   
### Containers/Pods Monitoring

1. Average CPU 
2. Average Memory
3. Pods Running
4. Pods Failed
5. Pods Pending
6. Pods Restarting
7. Cluster Overall Memory Usage
8. Cluster Overall CPU Usage

### Uptime Monitoring
1. Applications uptime
2. URL uptime
3. Container Uptime (Running < 1)
4. Utility machines uptime (Jenkins, Grafana)

### Nodes Monitoring
1. CPU
2. Disk
3. Memory
4. Disk Writes
5. Disk Reads
6. Load Average

### Applocation Monitoring
1. 4XX Error Count
2. 5XX Error Count
3. Latency
4. Active Connections
5. Waiting Connections
