## Lab 2

### View core add-ons and deploy apps
### - including monitoring, logging facilities

- Monitoring – show CPU, mem, networking, disk, etc.
- Kubectl execution - MySQL Pod Testing
- Multi-tiered application - Hipster App
- Helm example - Helmet App
- Traefik example – Jenkins
- Persistent storage discussion – OpenEBS

#### Monitoring – show CPU, mem, networking, disk, etc.

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


#### Traefik example - Jenkins

Show main cluster (with Jenkins installed on it)