# Kubernetes
Initiation kubernetes 

#Documentation
https://en.wikipedia.org/wiki/Kubernetes
https://kubernetes.io/
https://birthday.play-with-docker.com/kubernetes-docker-desktop/
https://www.youtube.com/watch?v=PKmtoM3A1cE&t=748s

#repository tutorial 
https://github.com/dockersamples/example-voting-app

#Shell 

>kubectl get nodes -o wide
NAME             STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                       CONTAINER-RUNTIME
docker-desktop   Ready    control-plane   18h   v1.30.5   192.168.65.3   <none>        Docker Desktop   5.15.167.4-microsoft-standard-WSL2   docker://27.4.0

création du namespace
>kubectl create namespace vote

>kubectl get namespaces
NAME              STATUS   AGE
default           Active   17h
kube-node-lease   Active   17h
kube-public       Active   17h
kube-system       Active   17h
vote      

>Voir les ressources d’un namespace spécifique :
kubectl get pods -n mon-namespace
kubectl get services -n mon-namespace

>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/refs/heads/main/k8s-specifications/vote-deployment.yaml -n vote

>kubectl get deployments -n vote
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
vote   1/1     1            1           17h

>kubectl delete service vote -n default

>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/vote-service.yaml -n vote

>kubectl get services -n vote
NAME   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
vote   NodePort   10.102.172.236   <none>        8080:31000/TCP   34s

That’s a Python application running in a Docker container, being managed by Kubernetes. 
url app : http://localhost:31000/

Par exemple, si un service nommé vote existe dans le namespace default, tu peux le supprimer avec :
>kubectl delete service vote -n default


https://github.com/dockersamples/example-voting-app/blob/main/k8s-specifications/result-service.yaml
https://github.com/dockersamples/example-voting-app/blob/main/k8s-specifications/result-deployment.yaml


Resiliance 
Print the container ID for the result app:

docker container ls -f name='k8s_result*' --format '{{.ID}}'
And now remove that container:

docker container rm -f $(docker container ls -f name='k8s_result*' --format '{{.ID}}')
Check back on the result app in your browser at http://localhost:5001 and you’ll see it’s still working. Kubernetes saw that the container had been removed and started a replacement straight away.

Print the result container ID again:

docker container ls -f name='k8s_result*' --format '{{.ID}}'
And you’ll see it’s a new container. Kubernetes makes sure the running app always matches the desired state in the application YAML file.


exemple cluster 

Cluster Kubernetes
├── Node 1
│   ├── Pod A (Namespace: dev)
│   ├── Pod B (Namespace: prod)
│   ├── Pod C (Namespace: test)
├── Node 2
│   ├── Pod D (Namespace: dev)
│   ├── Pod E (Namespace: prod)
│   ├── Pod F (Namespace: test)



