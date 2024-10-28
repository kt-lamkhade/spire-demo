https://spiffe.io/docs/latest/try/getting-started-k8s/

https://github.com/spiffe/spire/blob/main/README.md
https://github.com/spiffe/spire-tutorials/tree/main/k8s/quickstart

1. #Configure Kubernetes Namespace for SPIRE Components
   
kubectl apply -f spire-namespace.yaml


3. Configure SPIRE Server
   
Create Server Bundle Configmap, Role & ClusterRoleBinding

$ kubectl apply -f server-account.yaml  -f spire-bundle-configmap.yaml -f server-cluster-role.yaml


Create Server Configmap

$ kubectl apply  -f server-configmap.yaml  -f server-statefulset.yaml -f server-service.yaml

#AKIAZI2LGIRRH6MIDEV-2
#QaHReSv3XE/Lyv3wuUTdo80DeYET4vlsj6FrF5G-8

