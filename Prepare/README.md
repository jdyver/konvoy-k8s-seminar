## Prepare

### Required setup prior to session

#### Lab 1 / all

Setup terminal PS1 for easy transitioning
i.e.
```
mesosphere 201907 $ export PS1="K-UPGRADE \W $ "
```

```
K-UPGRADE konvoy_v0.3.0 $ ./konvoy up -y
```

Create OpenEBS cluster
Reference: https://hackmd.io/VEqmKzAjSZujETYO0enHGA?both

```
./konvoy init --provisioner=aws --cluster-name=toronto-konvoy1
```

Edit cluster.yaml:
 > nodepools/control plane > machine: > imageId: ami-0f551c319ca0f39a8
```
nodePools:
  - name: worker
    count: 4
    machine:
      imageId: ami-0f551c319ca0f39a8
      rootVolumeSize: 80
```

 > spec: > aws: > tags: > (define) owner: and expiration:
```
    tags:
      owner: jamesdyckowski
      expiration: 8h
```

```
./konvoy up
```

Create / Add EBS in AWS


```
curl -O https://openebs.github.io/charts/openebs-operator-1.0.0.yaml
```

Edit openebs-operator-1.0.0.yaml
 exclude line and change device mapping

 ```
kubectl apply -f openebs-operator-1.0.0.yaml
```

Deploy Jenkins:
```
     kubectl create ns jenkins

     kubectl create rolebinding default-admin --clusterrole=admin --serviceaccount=jenkins:default --namespace=jenkins

     kubectl create -f ./Files/k8s-jenkins-pvc.yaml

     kubectl get services -n jenkins

     kubectl get pods -n jenkins

     kubectl logs -n jenkins jenkins-XXXX

     URL: <konvoy master node URL>/jenkins
```

#### Lab 2

Preinstall:
- konvoy-upgrade-cluster: Hipster App
- konvoy-scale-cluster: MySQLReplicationExample

#### Lab 3

1. Build konvoy-upgrade cluster
```
./konvoy init --provisioner=aws --cluster-name=toronto-konvoy-upgrade
```

Edit Cluster.yaml
 > spec: > kubernetes: > version: 1.14.2

```
./konvoy up -y
```

Add workload

2. Build konvoy-scale cluster
```
./konvoy init --provisioner=aws --cluster-name=toronto-konvoy-scale
```

Edit Cluster.yaml
 > spec: > nodePools: > count: 3

```
./konvoy up -y
```

Add workload

[Go to Lab 1](https://github.com/jdyver/konvoy-k8s-seminar/tree/master/Lab1)
