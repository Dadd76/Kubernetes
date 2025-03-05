# Kubernetes
Initiation kubernetes 

#Documentation
https://en.wikipedia.org/wiki/Kubernetes
https://kubernetes.io/
https://birthday.play-with-docker.com/kubernetes-docker-desktop/
https://www.youtube.com/watch?v=PKmtoM3A1cE&t=748s
service : https://www.youtube.com/watch?v=T4Z7visMM4E&t

#repository tutorial 
https://github.com/dockersamples/example-voting-app
https://github.com/dockersamples/example-voting-app/tree/main
https://hub.docker.com/

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

>>>>votting app

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

>>>>result  app

>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/result-deployment.yaml -n vote

>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/result-service.yaml -n vote

>kubectl -n vote get pods
NAME                     READY   STATUS    RESTARTS   AGE
result-d8c4c69b8-xx8xb   1/1     Running   0          6m40s
vote-69cb46f6fb-8ftlk    1/1     Running   0          2d16h

>kubectl get services -n vote
NAME     TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
result   NodePort   10.107.149.136   <none>        8081:31001/TCP   6m45s
vote     NodePort   10.102.172.236   <none>        8080:31000/TCP   2d16h

url app : http://localhost:31001/

>>>>db
>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/db-deployment.yaml -n vote
>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/db-service.yaml -n vote

>>>>redis
>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/redis-deployment.yaml -n vote
>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/redis-service.yaml -n vote

>>>>>worker
>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/worker-deployment.yaml -n vote
>kubectl apply -f https://raw.githubusercontent.com/dockersamples/example-voting-app/main/k8s-specifications/worker-service.yaml -n vote

en un coup 
>kubectl create -f k8s-specifications/

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

> kubectl config view --minify
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://kubernetes.docker.internal:6443
  name: docker-desktop
contexts:
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: docker-desktop
kind: Config
preferences: {}
users:
- name: docker-desktop
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED

> kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://kubernetes.docker.internal:6443
  name: docker-desktop
contexts:
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: docker-desktop
kind: Config
preferences: {}
users:
- name: docker-desktop 
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED


 Comment connecter plusieurs nœuds à un cluster ?
Si tu veux ajouter plusieurs machines (workers) à ton cluster, voici les étapes :

1️⃣ Récupérer le token de connexion
Sur le nœud maître, exécute :

sh
Copier
Modifier
kubeadm token create --print-join-command
Cela te donnera une commande ressemblant à :

sh
Copier
Modifier
kubeadm join master-node-ip:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
2️⃣ Exécuter la commande sur chaque machine worker
Sur chaque nœud worker, exécute cette commande générée :

sh
Copier
Modifier
kubeadm join master-node-ip:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
Cela va connecter le worker au cluster.

3️⃣ Vérifier que les nœuds sont bien ajoutés
Sur le nœud maître, exécute :

sh
Copier
Modifier
kubectl get nodes
Si tout fonctionne, tu verras une liste avec :

pgsql
Copier
Modifier
NAME             STATUS   ROLES                  AGE   VERSION
master-node      Ready    control-plane,master   10m   v1.30.5
worker-node-1    Ready    <none>                 5m    v1.30.5
worker-node-2    Ready    <none>                 3m    v1.30.5

Installer Kubernetes Dashboard
Lance cette commande pour le déployer :

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml





