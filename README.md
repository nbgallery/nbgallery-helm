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

## Solr configuration

The files that need to be mounted to /var/solr/data is as such:

```bash
$ tree
.
|-- default
|   |-- conf
|   |   `-- managed-schema
|   |-- core.properties
|   |-- data
|   |-- lang (directory - Removed all these from the output)
|   |-- managed-schema
|   |-- params.json
|   |-- protwords.txt
|   |-- solrconfig.xml
|   |-- stopwords.txt
|   `-- synonyms.txt
|-- solr.xml
`-- zoo.cfg
```
It's set up to use configmaps for each directory. Since a configmap is flat, the whole tree can't be stored in just one. Each directory's configmap is mounted to an init container, as well as the data pvc for Solr. The init container then copies all these configmaps over to the data pvc for Solr:

```bash
mkdir /tmp/configs/default
mkdir /tmp/configs/default/conf
mkdir /tmp/configs/default/lang
mkdir /tmp/configs/default/data
cp -L /tmp/solr/solr.xml /tmp/configs
cp -L /tmp/solr/zoo.cfg /tmp/configs
cp -L -r /tmp/default/core.properties /tmp/configs/default
cp -L -r /tmp/default/managed-schema /tmp/configs/default
cp -L -r /tmp/default/params.json /tmp/configs/default
cp -L -r /tmp/default/protwords.txt /tmp/configs/default
cp -L -r /tmp/default/solrconfig.xml /tmp/configs/default
cp -L -r /tmp/default/stopwords.txt /tmp/configs/default
cp -L -r /tmp/default/synonyms.txt /tmp/configs/default
cp -L -r /tmp/default-conf/managed-schema /tmp/configs/default/conf
cp -L -r /tmp/default-lang/*.txt /tmp/configs/default/lang
```
The reason we don't mount the configmaps directly to the Solr container is due to configmaps not being able to mount to where a volume is already mounted. It also copies each file individually because there is some configmap funkiness that happens with symlinks. 
