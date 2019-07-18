# Docker commands

## Commands to create a new Docker image
```
docker build .
docker images
docker run -p 3000:3000 -it IMAGE_ID (from <none> tag)`
```

### tag default 'lastest'
```
docker build -t rodrigoguedes/docker-hello .
docker push rodrigoguedes/docker-hello
```

### add tag
```
docker tag rodrigoguedes/docker-hello rodrigoguedes/
docker-hello:0.0.1
docker push rodrigoguedes/docker-hello:0.0.1
```

### run image locally
```
docker images | grep rodrigoguedes
docker run -p 3000:3000 -it IMAGE_ID (from rodrigoguedes)
```

# Kubernetes commands

## Command used to docker-for-desktop
```
kubectl get nodes
kubectl config get-contexts
kubectl config use-context docker-for-desktop
kubectl get nodes
kubectl run hello-kubernetes --image=k8s.gcr.io/echoserver:1.10 --port=8080
kubectl expose deployment hello-kubernetes --type=NodePort
kubectl get services hello-kubernetes
curl http://localhost:30257  (8080:30257 got from previous step)
```

## Useful Commands

| Command                                                              | Description                                      |
| -------------------------------------------------------------------- | ------------------------------------------------ |
| `kubectl expose pod <pod> --port=444 --name=frontend`                | Expose the port of a pod (creates a new service) |
| `kubectl port-forward <pod> 8080`                                    | port forward the exposed pod port to your local machine |
| `kubectl attach <podname> -i`                                        | Attach to the pod |
| `kubectl exec <pod> -- command`                                      | Execute a command on the pod |
| `kubectl labels pods <pod> mylabel=awesome`                          | Add a new label to a pod |
| `kubectl run -i --tty busybox --image=busybox --restart=Never -- sh` | Run a shell in a pod - very useful for debuggin |


## Create the pod on kubernetes
```
kubectl create -f pod-hello.yml
kubectl get pod
kubectl describe pod nodekuberneteshello.example.com
kubectl port-forward nodekuberneteshello.example.com 8081:3000
curl localhost:8081
kubectl expose pod nodekuberneteshello.example.com --type=NodePort --name kuberneteshello-service
kubectl get service
```
to see the logs of pod
```
kubectl attach nodekuberneteshello.example.com -i
```
list all files that are inside of `app` folder
```
kubectl exec nodekuberneteshello.example.com -- ls /app
kubectl describe service kuberneteshello-service
``` 

## Use busybox to connect on cluster and execute a GET HTTP Request
```
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh
~ telnet 10.1.0.23 3000   -> 10.1.0.23 get from (kubectl describe service kuberneteshello-service) attribute Endpoints
~ GET /
```

## Replication Controller - Scale pods
```
kubectl create -f service-hello-controller.yml
kubectl get pods
kubectl describe pod hello-controller-lg26x
kubectl delete pod hello-controller-lg26x
kubectl get pods
```
Scale to 4 using file
```
kubectl scale --replicas=4 -f service-hello-controller.yml
kubectl get rc
```

Scale to 1 using name
```
kubectl scale --replicas=1 rc/hello-controller
kubectl delete rc/hello-controller
```

## Deployments
```
kubectl create -f service-hello-deployment.yml
kubectl get deployments
kubectl get pods --show-labels
kubectl rollout status deployment/hello-deployment
kubectl expose deployment hello-deployment --type=NodePort
kubectl get service
kubectl describe service hello-deployment
kubectl set image deployment/hello-deployment k8s-demo=rodrigoguedes/docker-demo:0.0.2
```