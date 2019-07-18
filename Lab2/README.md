## Lab 2

### View core add-ons and deploy apps: including monitoring, logging facilities

- Monitoring – show CPU, mem, networking, disk, etc.
- Kubectl execution - MySQL Pod Testing
- Multi-tiered application - Hipster App
- Helm example - Helmet App
- Traefik example – Jenkins
- Persistent storage discussion – OpenEBS

#### Monitoring – Show CPU, mem, networking, disk, etc.

Grafana dashboards to show:
CPU / Mem - Cluster/Node
Disk - Node
Networking - USENode (very bottom)

#### Kubectl execution - MySQL Pod Testing

github.com/jdyver/MySQLReplicationExample

To bring up:
```
bash MySQLReplicationExample.sh up
```

To automatically step through tests:
```
bash MySQLReplicationExample.sh test
```

In a separate terminal run the loop to show which servers are getting hits:
- Exit with CTRL-C
```
 Scripts $ kubectl run mysql-client-loop --image=mysql:5.7 -i -t --rm --restart=Never --\
 bash -ic "while sleep 1; do mysql -h mysql-read -e 'SELECT @@server_id,NOW()'; done"
If you don't see a command prompt, try pressing enter.
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         100 | 2019-06-12 21:52:27 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         102 | 2019-06-12 21:52:28 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2019-06-12 21:52:29 |
+-------------+---------------------+
^C
```

#### Multi-tiered application - Hipster App

Reference: https://github.com/GoogleCloudPlatform/microservices-demo

```
git clone https://github.com/GoogleCloudPlatform/microservices-demo
```

cd microservices-demo

```
kubectl apply -f ./release/kubernetes-manifests.yaml
```

Find AWS LB URL:
```
kubectl get service
```

Go to URL above

```
kubectl delete -f ./release/kubernetes-manifests.yaml
```

#### Helm example - Helmet App

https://daemonza.github.io/2017/02/20/using-helm-to-deploy-to-kubernetes/

Sample starter commands
```
$ helm help
$ helm ls
```

Initialize Helm
```
helm init
```

Pull helmet repo
```
git clone https://github.com/daemonza/helmet.git; cd helmet/helmet-chart
```

Review contents
```
K-UPGRADE helmet-chart $ ls
Chart.yaml	templates	values.yaml

K-UPGRADE helmet-chart $ cat Chart.yaml
apiVersion: v1
description: Helmet is a simple helm repository
name: helmet-chart
version: 0.0.1

K-UPGRADE helmet-chart $ cat values.yaml
# Default values for helmet-chart.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.
replicaCount: 1
image:
  repository: daemonza/helmet
  tag: latest
  pullPolicy: Always
service:
  name: helmet
  type: NodePort
  externalPort: 1323
  internalPort: 1323
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

Create self contained zip
```
helm package . --debug
```

Find local Helm repo
```
K-UPGRADE helmet-chart $ ls ~/.helm/repository/local/
helmet-chart-0.0.1.tgz	index.yaml
```

```
K-UPGRADE helmet-chart $   export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services roiling-numbat-helmet-ch)
K-UPGRADE helmet-chart $   export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
K-UPGRADE helmet-chart $   echo http://$NODE_IP:$NODE_PORT/login
http://10.0.128.134:32345/login
```

```
K-UPGRADE helmet-chart $ kubectl get services
NAME                       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
kubernetes                 ClusterIP   10.0.0.1      <none>        443/TCP          48m
roiling-numbat-helmet-ch   NodePort    10.0.62.198   <none>        1323:32345/TCP   3m17s
```

```
K-UPGRADE helmet-chart $ helm search helmet
NAME              	CHART VERSION	APP VERSION	DESCRIPTION
local/helmet-chart	0.0.1        	           	Helmet is a simple helm repository
```

#### Traefik example - Jenkins

Show main cluster (with Jenkins installed on it)