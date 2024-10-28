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

aws eks update-kubeconfig --name spire-demo --region=us-east-1

# https://github.com/devopsproin/certified-kubernetes-administrator/tree/main/PV-PVC

helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver
helm repo update


helm upgrade --install aws-ebs-csi-driver \
    --namespace kube-system \
    aws-ebs-csi-driver/aws-ebs-csi-driver
    
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver    
