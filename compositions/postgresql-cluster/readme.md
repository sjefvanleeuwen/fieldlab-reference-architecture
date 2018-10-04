# PostgreSQL Cluster
(On Microsoft AZURE AKS)

As part of the persistent datalayer, the fieldlab uses the PostgreSQL RDBMS. In operation the database is a blackbox and agnostic from the ORM layer. We develop code first. In the code-first approach, EF Core API creates the database and tables using migration based on the conventions and configuration provided in your domain classes. This approach is useful in Domain Driven Design (DDD).

## System Requirements

Please run the cluster on a minimum of 2 vCPU's / 4GB for the containers to run properly, or you might get out of memory error's because of other services, such as the api-layer running in the same space. The cluster initializes with a 400MB persistent volume.

When reinstalling the PostgreSQL cluster, please remove all the "Secrets" and "Persistent Volume Claims". You can delete these using the kubernetes dashboard.

```
$ az aks browse --resource-group k8slab --name wigo4it-k8
```

## Install Helm

To install the cluster make sure you have HELM installed. We will be using HELM as our packagemanager for Kubernetes. You can install Helm using snap. Tested on KDE (Kubuntu 18.04)

For more information on HELM, Get started with the [Quick Start guide](https://docs.helm.sh/using_helm/#quickstart-guide) or plunge into the [complete documentation](https://docs.helm.sh/)

```
$ sudo snap install helm
```
This wil install helm client latest version. If you get an error such as below, during further operation with helm while setting up your postgresql cluster:

```
incompatible versions client[v2.11.0] server[v2.10.0]
```

Then upgrade the helm server version (Tiller) to the latest version and reinitialize for a ready tiller pod as follows:

```
$ helm init --upgrade
$HELM_HOME has been configured at /home/**********/.helm.
Tiller (the Helm server-side component) has been upgraded to the current version.
Happy Helming!

$ helm init
```

Not tested, but if so desired, you can also install Helm through brew, chocolate or gofish.

```
$ brew install kubernetes-helm

or

$ choco install kubernetes-helm

or

$ gofish install helm
```

```
$ helm init
```

From within the ./postgresql-cluster directory (where this readme.md is located), issue the following command:

```
$ helm install ./primary-replica
```

This will install a primary postgresql for write puposes and one replica as the read only database for which you can scale to *n* replicas.

The output should be as follows:

```
NAME:   crabby-ibex
LAST DEPLOYED: Thu Oct  4 13:00:18 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Secret
NAME              AGE
pgprimary-secret  2s
pgroot-secret     2s
pguser-secret     2s

==> v1/PersistentVolume
primary-pv  2s

==> v1/PersistentVolumeClaim
primary-pvc  2s

==> v1/Service
primary  1s
replica  1s

==> v1/Pod
primary  1s

==> v1beta1/Deployment
replica  1s

==> v1/Pod(related)

NAME                      READY  STATUS             RESTARTS  AGE
replica-79ccc5bddf-8zdl4  0/1    ContainerCreating  0         1s


NOTES:
Thank you for installing primary-replica chart.

Your release is named crabby-ibex.

To learn more about the release, try:

  $ helm status crabby-ibex
  $ helm get crabby-ibex
```

Amongst the existing services in the cluster that have been deployed as part of the fieldlab reference architecture, you should now be able to see the following services on the cluster.

```
$ kubectl get services

NAME         TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                        AGE
api-layer    LoadBalancer   10.0.108.216   40.114.197.8   5080:31753/TCP,5091:30762/TCP,8088:31441/TCP   3d
kubernetes   ClusterIP      10.0.0.1       <none>         443/TCP                                        3d
primary      LoadBalancer   10.0.126.6     13.69.51.195   5432:30130/TCP                                 17m
replica      LoadBalancer   10.0.197.60    13.69.63.60    5432:31731/TCP                                 17m
```

Services **replica** and **master** are now available. Please wait a couple of minutes for them to sync up.

## Testing the Master/Slave

Please install the psql client if you have not done so.

```
$ sudo apt install postgresql-client
```

To check the synchronization status between the master / slave you can issue the following command:

```
$ psql -h master -U postgres postgres -c 'table pg_stat_replication'
```

To test basic operation and replication, create data on the master node:

```
$ psql -h <external-ip-master> -U postgres postgres -c 'create table test (id int)'
Password for user postgres:
CREATE TABLE

$ psql -h <external-ip-master> -U postgres postgres -c 'insert into test values(1)'
Password for user postgres:
INSERT 0 1

psql -h <external-ip-replica> -U postgres postgres -c 'table test'
Password for user postgres:
 id
----
  1
(1 row)

```

As shown in the example session, you should see the replicated row appearing on the replica.

A full description on the "Chart" can be found [here](./primary-replica/README.md).
