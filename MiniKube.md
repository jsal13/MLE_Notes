# MiniKube

## Tutorial with MiniKube
For the duration of [this tutorial](https://minikube.sigs.k8s.io/docs/start/), we have the **admin powershell** up, and we use `minikube dashboard` in the background on one of them to check stuff out.

- Created service with 
````shell
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
````

- Exposed the port with
```shell
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```
This exposes the port, but doesn't forward it.

- We forwarded the port with
```shell
kubectl port-forward service/hello-minikube 7080:8080
```
which allowed us to open it in the browser.