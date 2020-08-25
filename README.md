# nbgallery-helm
Helm Chart for deploying NBGallery to a Kubernetes Cluster

## Installation

Configure a secret such as
```bash
kubectl --namespace nbgallery create secret generic mysql-password \
    --from-literal=mysql-password=${MYSQL_PASSWORD} \
    --from-literal=mysql-root-password=${MYSQL_ROOT_PASSWORD}
```
Clone the repo, and then run
```bash
helm install nbgallery .
```
And nbgallery will be deployed on your kubernetes cluster.
