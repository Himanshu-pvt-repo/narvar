Objective: The take home assignment is to build a local cluster using minikube, docker desktop or kind, and host a simple hello world app, and add in cluster monitoring for the app. You should check in the code and any documentation to a git repo for review and send us the link to the repo.

Steps:

1. Fire the below command to install the minikube and start the local Kubernetes cluster.

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube && minikube start

2. Use the HelloWorld helm chart to run the application.

helm install myworld helloworld

Get the Nodeport Service URL by using the below command.

minikube service myworld-helloworld --url

Paste the URL to any browser, it will show the HelloWorld page.
