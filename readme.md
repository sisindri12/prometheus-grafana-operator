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
9. by default aws cloud shell don't have helm commands. so please install helm and use below   
hem install:
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
10. By using below commands add add Prometheus helm charts and install prometheus
 helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
 helm repo update
 helm repo add stable https://charts.helm.sh/stable
 helm install prometheus prometheus-community/kube-prometheus-stack
 helm list
11. After instaltion check pods are running or not
 kubectl get pods
