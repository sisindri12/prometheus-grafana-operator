In the repo we are using the third way. We will easily deploy Prometheus and Grafana using Helm chart for Prometheus Operator.

**In general there are 3 ways to do the setup:**
1. Create all the configuration files yourself 👩🏻‍💻
This way is pretty inefficient and it's a lot of effort.
2. Using a Kubernetes Operator 😎
In this option you would go and find an operator for Prometheus and deploy it in the cluster using the configuration files of the operator. It is more efficient.
3. Using Helm chart to deploy the Prometheus Operator 🚀
This is the most efficient way. Prometheus Operator has a Helm chart that is maintained by the Helm community. You can use this Helm chart to deploy the operator.
So, Helm will do the initial setup. Operator will then manage the running Prometheus setup

**Steps:**
1. Installed  EKS cluster in aws by using EKS automode. (https://docs.aws.amazon.com/eks/latest/userguide/automode.html)
2. After installing cluster use the aws cli or any terminal to isntall and configure prometheus operator.
3. I am using aws cli here to reduce the time for installing all required dependencies
4. Use below commands to connect aws
5. command: aws configure
   It will ask for accesskeyid and secretkey and region and format
6. once succesfully connected to aws by using below commands check the connectivity to deployed eks cluster.
7. aws s3 ls
8. In aws cloud shell use below commands to connect to eks cluster with updated kubeconfig and list the nodes and pods in the cluster.
   
aws eks list-clusters --region <region>
aws eks list-clusters --region us-east-1
aws eks update-kubeconfig --region <region> --name <cluster-name>
aws eks update-kubeconfig --region us-east-1 --name prometheus-grafana-cluster
kubectl get nodes
kubectl get pods --all-namespaces

10. by default aws cloud shell don't have helm commands. so please install helm and use below
    
hem install:
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

12. Using the commands below, add Prometheus helm charts and install Prometheus.
    
 helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
 helm repo update
 helm repo add stable https://charts.helm.sh/stable
 helm install prometheus prometheus-community/kube-prometheus-stack
 helm list
 
14. After installation check pods are running or not.
    
 kubectl get pods

15. To access grafana UI locally use port-forward command:

    kubectl port-forward deployment/prometheus-grafana 3000
16. Similarly same for prometheus UI
    kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090

===
By using above we have installed prometheus operator and it consista of grafana and alertmanager deployments.
===

NOW we can deploy one third party application and by using prometheus and grafana we can scrape metrics for that and visualize in grafana

===
Install Mongodb-exporter
add repos:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

install chart:
helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter 

===
Below command will help to enable the service monitor. so Prometheus can scrape metrics from exporters:

helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter - f values.yaml --set serviceMonitor.enabled=true

install chart with fixed version:

helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter --version "2.8.1"

Link to chart:
[https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-mongodb-exporter]

***Install mongodb application:***

kubectl apply -f mongodb.yaml

***Grafana Dashboard credentials:***

user: admin
pwd: prom-operator (from values.yaml file set as default)

***Mongodb-exporter:***

kubectl port-forward service/mongodb-exporter-prometheus-mongodb-exporter 9216  

