<!-- Create a local registry in kubernetes cluster hosted in minikube -->

kubectl run registry --image=registry:2 --port=5000

kubectl expose deployment registry --type=NodePort

---------------------------------------------------------------------------------------------------------
Microsoft Windows [Version 10.0.21996.1]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>minikube ip -p k8scluster2
172.23.215.15

C:\Windows\system32>kubectl get all
NAME           READY   STATUS    RESTARTS   AGE
pod/registry   1/1     Running   0          5m52s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          7h44m
service/registry     NodePort    10.106.211.85   <none>        5000:30124/TCP   4m1s

minikube ip > 172.23.215.15:30124

---------------------------------------------------------------------------------------------------------

<!-- Got to docker settings > Docker enginer in Docker desktop windows  -->
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



<!-- Build Local Docker Image -->
docker build -t custom-controller:v2 .

<!-- Tag Image to local Registry -->

docker tag custom-controller:v1 172.23.215.15:30124/custom-controller:v2

<!-- Push Image to loca registry -->

docker push 172.23.215.15:30124/custom-controller:v2

<!-- In the image put the cluster ip of the local registry service -->

NAMESPACE     NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP                  7h55m
default       service/registry     NodePort    10.106.211.85   <none>        5000:30124/TCP           15m

<!-- Since the CustomeCRDPOD is a cluster ip it wont be able to reach nodeport service  -->
10.106.211.85:30124/custom-controller:v2

<!-- Validate if the custom config map is created or not  -->

C:\Windows\system32>kubectl get ccm
NAME                          AGE
my-custom-resource-instance   2m37s
