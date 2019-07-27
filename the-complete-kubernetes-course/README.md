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

## Dashboard
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta1/aio/deploy/recommended.yaml
kubectl proxy
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.
# To get the Token
kubectl -n kube-system get secret | grep deployment-controller-token
kubectl -n kube-system describe secret deployment-controller-token-<GENERATED ID>
kubectl -n kube-system describe secret deployment-controller-token-mvtxk | grep token
```

## Command used to docker-for-desktop
```
kubectl cluster-info
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
kubectl create -f hello-kubernentes-pod/pod-hello.yml
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
kubectl create -f hello-kubernentes-pod/service-hello-controller.yml
kubectl get pods
kubectl describe pod hello-controller-lg26x
kubectl delete pod hello-controller-lg26x
kubectl get pods
```
Scale to 4 using file
```
kubectl scale --replicas=4 -f hello-kubernentes-pod/service-hello-controller.yml
kubectl get rc
```

Scale to 1 using name
```
kubectl scale --replicas=1 rc/hello-controller
kubectl delete rc/hello-controller
```

## Useful Commands - Deployments
| Command                                                              | Description                                      |
| -------------------------------------------------------------------- | ------------------------------------------------ |
| `kubectl get deployments`                                            | Get information on current deployments   |
| `kubectl get rds`                                                    | Get information about the replica sets |
| `kubectl get pods --show-labels`                                     | get podsm and also show labels attached to those pods |
| `kubectl rollout status deployment/hello-deployment`                 | Get deployment status |
| `kubectl set image deployment/hello-deployment k8s-demo=rodrigoguedes/docker-hello:0.0.2` | Run k8s-demo with the image label version 2 |
| `kubectl edit deployment/hello-deployment`                           | Edit the deployment object |
| `kubectl rollout status deployment/hello-deployment`                 | Get the status of the rollout |
| `kubectl rollout history deployment/hello-deployment`                | Get the rollout history |
| `kubectl rollout undo deployment/hello-deployment`                   | Rollback to previous version |
| `kubectl rollout undo deployment/hello-deployment --to-revision=2`   | Rollback to any version version |

## Deployments
```
kubectl create -f hello-kubernentes-pod/service-hello-deployment.yml
kubectl get deployments
kubectl get pods --show-labels
kubectl rollout status deployment/hello-deployment
kubectl expose deployment hello-deployment --type=NodePort
kubectl get service
kubectl describe service hello-deployment
kubectl set image deployment/hello-deployment k8s-demo=rodrigoguedes/docker-hello:0.0.2
kubectl rollout history deployment/hello-deployment
kubectl rollout undo deployment/hello-deployment
kubectl rollout undo deployment/hello-deployment --to-revision=2
kubectl edit deployment/hello-deployment
```

## Services
```
kubectl create -f hello-kubernentes-pod/pod-hello.yml
kubectl create -f hello-kubernentes-pod/service-hello.yml
kubectl describe service hello-service
kubectl get service (or) kubectl get svc
kubectl delete service hello-service
```

## Running Wordpress on Kubernetes (no volumes)
```
kubectl create -f wordpress-kubernetes/wordpress-secrets.yml
kubectl create -f wordpress-kubernetes/wordpress-single-deployment-no-volumes.yml
kubectl get pods
kubectl describe pod wordpress-deployment-6f47769b85-v9tgp
kubectl create -f wordpress-kubernetes/wordpress-service.yml
kubectl proxy

curl http://localhost:31001
or
open http://localhost:31001/
```

## Name space kube-system
```
kubectl get pods --namespace=kube-system
```

## Create a Service Discovery
```
kubectl create -f service-discovery/secrets.yml
kubectl create -f service-discovery/database.yml
kubectl create -f service-discovery/database-service.yml
kubectl create -f service-discovery/helloworld-db.yml 
kubectl create -f service-discovery/helloworld-db-service.yml
kubectl get pods
kubectl logs hello-deployment-774ff95d7d-dwfpz
kubectl get services
curl -X GET http://localhost:31186/
curl -X GET http://localhost:31186/
kubectl exec database -it -- mysql -D helloworld -u root --password=rootpassword -e "select * from visits;"
```

## ConfigMap
```
kubectl create configmap nginx-config --from-file=configmap/reverseproxy.conf
kubectl describe configmap nginx-config
kubectl get configmap
kubectl get configmap nginx-config -o yaml
kubectl create -f configmap/nginx.yml
kubectl create -f configmap/nginx-service.yml
kubectl get pods
kubectl get services
kubectl proxy
curl http://localhost:30311 -vvv
kubectl exec -it helloworld-nginx -c nginx -- bash
cat /etc/nginx/conf.d/reverseproxy.conf
kubectl exec -it helloworld-nginx -c k8s-demo -- bash
```

## Ingress
```
v1 - running in minikube
kubectl create -f ingress/ingress.yml
kubectl create -f ingress/nginx-ingress-controller.yml
kubectl create -f ingress/echoservice.yml
kubectl create -f ingress/helloworld-v1.yml
kubectl create -f ingress/helloworld-v2.yml
kubectl get pod
kubectl get service
kubectl get ingress
kubectl get ingress helloworld-rules
edit /etc/hosts and add helloworld-v1.example.com and helloworld-v2.example.com
curl helloworld-v1.example.com
curl helloworld-v2.example.com

v2 - running in docker-for-desktop (LoadBalancer)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
or
kubectl apply -f ingress/nginx-ingress-controller-local.yaml
kubectl apply -f ingress/ingress-local.yml 
kubectl get svc -n ingress-nginx
kubectl get pods -n ingress-nginx
kubectl get service -n ingress-nginx
kubectl create -f ingress/ingress.yml
kubectl create -f ingress/helloworld-v1.yml
kubectl create -f ingress/helloworld-v2.yml
edit /etc/hosts and add helloworld-v1.example.com and helloworld-v2.example.com
curl helloworld-v1.example.com
curl helloworld-v2.example.com
```

## Volumes - Memory / Temporary disks
```
kubectl create -f volumes/storage-memory.yml 
kubectl exec -it memory-pd -c memory-pd -- bash
cd /memory-pd
or -> kubectl exec memory-pd -- ls -lh | grep memory-pd
kubectl describe pod memory-pd
```

## Volumes - Cloud Volumes - Google
```
Create Volume - Using AWS's EBS
# From the console, in Compute Engine, go to Disks. On this new screen, click on the Create Disk button. 
kubectl create -f storage-gce.yml
kubectl describe pod/test-gce
```

## Volumes - Cloud Volumes - AWS
```
Create Volume - Using AWS's EBS
aws ec2 create-volume --size 10 --region your-region --availability-zone your-zone --volume-type gp2 --tag-specifications 'ResourceType=volume, Tags=[{Key= KubernetesCluster, Value=kubernetes.domain.tld}]'
# This command will return a json so you need t0 get the VolumeId information for next command
kubectl get node
kubectl get pod
kubectl create -f volumes/helloworld-with-volume-aws.yml
```

## Volumes - Persistence Volume + Persistence Volume Claim + Deployment/Mysql
```
kubectl create -f volumes/mysql-persistence-volume.yml
kubectl get pv
kubectl describe pv my-pv
kubectl create -f volumes/mysql-persistence-volume-claim.yml
kubectl get pvc
kubectl describe pvc mysql-pvc
kubectl create -f volumes/mysql-deployment.yml
kubectl get pods
kubectl exec -it mysql-786468dccf-prm48 -- bash
ls /var/lib/mysql/data
ls -R ~/.docker/Volumes/mysql-pvc
tree ~/.docker/Volumes/mysql-pvc
```