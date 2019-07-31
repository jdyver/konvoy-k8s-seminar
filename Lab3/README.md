## Lab 3

### Executing Day 2 operations
### - upgrade, backup/restore, storage monitoring

- Backup etcd data
- Upgrade 1.14.2 – 1.14.3
- Scale cluster out
- Kafka / KUDO (time permitting)
    - In Grafana: Add Kafka grafana dashboard live
- Mayadata OpenEBS storage monitoring (mayaonline.io)

#### Backup etcd data

install velero commandline

brew install velero

Create backup:
```
velero --kubeconfig=admin.conf backup create pre-upgrade-backup
```

Wait for backup to finish:
```
velero describe backup
```

Ruin something (NOT where Velero stands):
kubectl delete namespace ####

To restore:
```
velero --kubeconfig=admin.conf restore create restore-pre-upgrade-backup --from-backup pre-upgrade-backup
```

Wait for restore to finish:
-  There are errors, ignore them
```
velero describe restore
velero restore get (?)
```

#### Upgrade 1.14.2 – 1.14.3
Initial state:
```
Scripts konvoy_v0.3.0 $ kubectl version --short=true
Client Version: v1.15.0
Server Version: v1.14.2
```

Edit cluster.yaml (konvoy-upgrade cluster)
 > spec: > kubernetes: > version: 1.14.3

```
konvoy upgrade kubernetes -y
```

Expected state:
```
Scripts konvoy_v0.3.0 $ kubectl version --short=true
Client Version: v1.15.0
Server Version: v1.14.3
```

#### Scale cluster out
Initial state:
```
Scripts konvoy_v0.3.0 $ kubectl get nodes
NAME                                         STATUS   ROLES    AGE   VERSION
ip-10-0-128-141.us-west-2.compute.internal   Ready    <none>   30m   v1.14.3
ip-10-0-128-230.us-west-2.compute.internal   Ready    <none>   77m   v1.14.3
ip-10-0-130-59.us-west-2.compute.internal    Ready    <none>   77m   v1.14.3
ip-10-0-192-182.us-west-2.compute.internal   Ready    master   98m   v1.14.3
ip-10-0-193-18.us-west-2.compute.internal    Ready    master   99m   v1.14.3
ip-10-0-195-46.us-west-2.compute.internal    Ready    master   97m   v1.14.3
```

Edit cluster.yaml (konvoy-scale cluster)
 > spec: > nodePools: > count: 5

Expected state:
```
Scripts konvoy_v0.3.0 $ kubectl get nodes
NAME                                         STATUS   ROLES    AGE   VERSION
ip-10-0-128-141.us-west-2.compute.internal   Ready    <none>   49m   v1.14.3
ip-10-0-128-230.us-west-2.compute.internal   Ready    <none>   96m   v1.14.3
ip-10-0-130-242.us-west-2.compute.internal   Ready    <none>   14m   v1.14.3
ip-10-0-130-247.us-west-2.compute.internal   Ready    <none>   14m   v1.14.3
ip-10-0-130-59.us-west-2.compute.internal    Ready    <none>   96m   v1.14.3
ip-10-0-192-182.us-west-2.compute.internal   Ready    master   98m   v1.14.3
ip-10-0-193-18.us-west-2.compute.internal    Ready    master   99m   v1.14.3
ip-10-0-195-46.us-west-2.compute.internal    Ready    master   97m   v1.14.3
```

#### Kafka / KUDO (time permitting) - In Grafana: Add Kafka grafana dashboard live

https://github.com/tbaums/kudo-kafka-konvoy-demo

#### Mayadata OpenEBS storage monitoring (mayaonline.io)

[ Picture of Mayadata ]
