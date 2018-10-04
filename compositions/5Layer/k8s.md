# Setup Kubernetes (AKS Azure)

## Setup your dashboard

Use the following steps to open the Kubernetes dashboard.

1. Open Azure CLI version 2.0.27 or higher. The version does not work in Cloud Shell and must be running on your local computer. [Instruction for installing Azure CLI](https://docs.microsoft.com/nl-nl/cli/azure/install-azure-cli?view=azure-cli-latest)

2. If you have not yet installed kubectl in CLI, run the following command:

```bash
$ az aks install-cli
Downloading client to "/usr/local/bin/kubectl" from "https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kubectl"
Please ensure that /usr/local/bin is in your search PATH, so the `kubectl` command can be found.
```
3. Get the references for your cluster with the following command:

```bash
$ az aks get-credentials --resource-group k8slab --name wigo4it-k8s
Merged "wigo4it-k8s" as current context in /home/*******/.kube/config
```

4. Open the Kubernetes dashboard with the following command:

```bash
$ az aks browse --resource-group k8slab --name wigo4it-k8s
az aks browse --resource-group k8slab --name wigo4it-k8s
Merged "wigo4it-k8s" as current context in /tmp/tmpmvcii05n
Proxy running on http://127.0.0.1:8001/
Press CTRL+C to close the tunnel...
Forwarding from 127.0.0.1:8001 -> 9090
Forwarding from [::1]:8001 -> 9090
Handling connection for 8001
```

Your default webbrowser will popup and will show the dashboard for your kubernetes cluster running on Azure

![k8s dashboard](./doc/k8s-dashboard.png)

## Deploying Kubernetes Fieldlab

Use the kubectl apply command to run the application

```bash
$ kubectl apply -f deploy-k8s.yml
```
To monitor progress, use the kubectl get service command with the --watch argument.

```bash
kubectl get service api-service --watch
```
Initially the EXTERNAL-IP for the azure-vote-front service appears as pending.

```pre
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
api-layer    LoadBalancer   10.0.42.17   <pending>     5080:30326/TCP,5091:30091/TCP,8088:31521/TCP   31s
kubernetes   ClusterIP      10.0.0.1     <none>        443/TCP                                        4h25m
```
```pre
Once the EXTERNAL-IP address has changed from pending to an IP address, use CTRL-C to stop the kubectl watch process.
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
api-layer    LoadBalancer   10.0.108.216   40.114.197.8   5080:31753/TCP,5091:30762/TCP,8088:31441/TCP   22mkubernetes   ClusterIP      10.0.0.1     <none>        443/TCP                                        4h25m
```

Now browse for example to the external IP address on port 5080, to see to the BRP openapi service.

You can find the deployment script [here](./deploy-k8s.yml)
