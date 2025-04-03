```markdown
# Setting Up a Local Registry in a Kubernetes Cluster Hosted on Minikube

## Step 1: Create a Local Registry

Run the following command to create a local registry in your Kubernetes cluster:

```bash
kubectl run registry --image=registry:2 --port=5000
```

Expose the deployment as a NodePort service:

```bash
kubectl expose deployment registry --type=NodePort
```

## Step 2: Retrieve Minikube IP and Verify the Registry

Get the Minikube IP address:

```bash
minikube ip -p k8scluster2
```

Example output:

```
172.23.215.15
```

Check the status of the registry:

```bash
kubectl get all
```

Example output:

```
NAME           READY   STATUS    RESTARTS   AGE
pod/registry   1/1     Running   0          5m52s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          7h44m
service/registry     NodePort    10.106.211.85   <none>        5000:30124/TCP   4m1s
```

The registry can be accessed at:

```
172.23.215.15:30124
```

## Step 3: Configure Docker Desktop for the Local Registry

Go to **Docker Desktop Settings > Docker Engine** and add the following configuration:

```json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "insecure-registries": ["172.23.215.15:30124"]
}
```

## Step 4: Build and Push a Local Docker Image

Build the Docker image:

```bash
docker build -t custom-controller:v2 .
```

Tag the image for the local registry:

```bash
docker tag custom-controller:v2 172.23.215.15:30124/custom-controller:v2
```

Push the image to the local registry:

```bash
docker push 172.23.215.15:30124/custom-controller:v2
```

## Step 5: Verify the Registry Service

Check the services in the cluster:

```bash
kubectl get svc
```

Example output:

```
NAMESPACE     NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP                  7h55m
default       service/registry     NodePort    10.106.211.85   <none>        5000:30124/TCP           15m
```

Ensure the NodePort IP and port are accessible:

```
172.23.215.15:30124/custom-controller:v2
```

## Step 6: Validate the Custom Config Map

Check if the custom config map is created:

```bash
kubectl get ccm
```

Example output:

```
NAME                          AGE
my-custom-resource-instance   2m37s
```
```
