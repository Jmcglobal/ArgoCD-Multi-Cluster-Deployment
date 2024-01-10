# EKS Setup

## EKS Clusters Creation

eksctl create cluster --name hub-cluster --region us-west-1

eksctl create cluster --name spoke-cluster-1 --region us-west-1

eksctl create cluster --name spoke-cluster-2 --region us-west-1

## EKS Clusters Deletion

eksctl delete cluster --name hub-cluster --region us-west-1

eksctl delete cluster --name spoke-cluster-1 --region us-west-1

eksctl delete cluster --name spoke-cluster-2 --region us-west-1

### Get Context

- kubectl config get-contexts >> This will display k8s cluster endpoints

- kubectl config current-context >> This will display the current cluster you are connected to

kubectl config use-conext <cluster-name>  Switch to the cluster to able to run command directory on the cluster, this is done when you have more than one cluster>

### Install ArgoCD, follow the docs and paste the command on get started

#### Docs

- https://argo-cd.readthedocs.io/en/stable/getting_started/


### ArgoCD docs on Github

- https://github.com/argoproj/argo-cd

#### Commands
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Change ArgoCD server to Insecure Mode

### In production environment, you must run argocd server on secure mode (https)

### Get Config map and Edit ConfigMap in argocd namespace

```
kubectl get cm -n argocd
```

- Edit "argocd-cmd-params-cm" 

```
kubectl edit cm argocd-cmd-params-cm  -n argocd
```
- Add the below configuration at the bottom of the config

```
data:
    server.insecure: "true"
```
- whole config be exactly below

```
apiVersion: v1
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cmd-params-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cmd-params-cm","namespace":"argocd"}}
  creationTimestamp: "2024-01-10T20:25:55Z"
  labels:
    app.kubernetes.io/name: argocd-cmd-params-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cmd-params-cm
  namespace: argocd
  resourceVersion: "7038"
  uid: 3b59622d-d2da-4167-89bf-239fb94e08ed
```

- Save the file

- To use NodePort on argocd Server

```
kubectl get svc -n argocd
kubectl edit svc argocd-server -n argocd
```

- scroll down and change the service type to NodePort

- Reach the server using NodePort public IP address and Argocd server port number

```
username = admin

password "
kubectl get secret argocd-initial-admin-secret -n argocd  -o yaml | grep password

echo <paste-the password stringe> | base64 -d; echo

```
Copy the password and paste

### To add external cluster, use argocd CLI

Docs: https://argo-cd.readthedocs.io/en/stable/cli_installation/

- To add cluster 

- login to argocd using cli to be able to add a cluster

argocd login --username <username> --password <password> <argocd-server IP and Port/domain name> --insecure
```
argocd login --username admin --password admin2024 54.153.10.45:32631 --insecure

argocd cluster add <cluster name you get from the contexts> --server <argocd-server IP address/domain name>

argocd cluster add Justice@spoke-cluster-1.us-west-1.eksctl.io --server 54.153.10.45:32631
```

- The cluster has been added successfully, you can confirm it by going to the  argocd web user interface > settings > clusters, and see the clustersdata:
    server.insecure: "true"
