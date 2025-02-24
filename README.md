# Kubernetes
Initiation kubernetes 

#Documentation
https://en.wikipedia.org/wiki/Kubernetes
https://kubernetes.io/
https://birthday.play-with-docker.com/kubernetes-docker-desktop/

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

url app : http://localhost:31000/

Par exemple, si un service nommé vote existe dans le namespace default, tu peux le supprimer avec :
>kubectl delete service vote -n default



