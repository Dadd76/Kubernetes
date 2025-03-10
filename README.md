# Kubernetes
Initiation kubernetes 

-----------------------------------------------------------------------------------------------------------------------------
Documentation
-----------------------------------------------------------------------------------------------------------------------------
https://en.wikipedia.org/wiki/Kubernetes
https://kubernetes.io/
https://birthday.play-with-docker.com/kubernetes-docker-desktop/
https://www.youtube.com/watch?v=PKmtoM3A1cE&t=748s
service : https://www.youtube.com/watch?v=T4Z7visMM4E&t

#repository tutorial 
https://github.com/dockersamples/example-voting-app
https://github.com/dockersamples/example-voting-app/tree/main
https://hub.docker.com/

-----------------------------------------------------------------------------------------------------------------------------
Activer kubernetes sur docker desktop
-----------------------------------------------------------------------------------------------------------------------------
https://birthday.play-with-docker.com/kubernetes-docker-desktop/

1. Install Docker Desktop
Docker Desktop is freely available in a community edition, for Windows and Mac. Start by downloading and installing the right version for you:
2. Enable Kubernetes
Settings > Kubernetes > enable Kubernets (swith on)
3. Check the state of your Docker Desktop cluster
> kubectl get nodes -o wide
NAME             STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                       CONTAINER-RUNTIME
docker-desktop   Ready    control-plane   18h   v1.30.5   192.168.65.3   <none>        Docker Desktop   5.15.167.4-microsoft-standard-WSL2   docker://27.4.0
   
-----------------------------------------------------------------------------------------------------------------------------
Commande shell 
-----------------------------------------------------------------------------------------------------------------------------

#obtenir la version de kubernetes 
> kubectl version
Client Version: v1.30.5
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.30.5

-----------------------------------------------------------------------------------------------------------------------------
Déploiement Vote app
-----------------------------------------------------------------------------------------------------------------------------
#Shell 

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

-----------------------------------------------------------------------------------------------------------------------------
Comment connecter plusieurs nœuds à un cluster ?
-----------------------------------------------------------------------------------------------------------------------------
Si tu veux ajouter plusieurs machines (workers) à ton cluster, voici les étapes :

1️⃣ Récupérer le token de connexion
Sur le nœud maître, exécute :
>kubeadm token create --print-join-command
Cela te donnera une commande ressemblant à :

>kubeadm join master-node-ip:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
2️⃣ Exécuter la commande sur chaque machine worker
Sur chaque nœud worker, exécute cette commande générée :

>kubeadm join master-node-ip:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
Cela va connecter le worker au cluster.

3️⃣ Vérifier que les nœuds sont bien ajoutés
Sur le nœud maître, exécute :

>kubectl get nodes
Si tout fonctionne, tu verras une liste avec :

NAME             STATUS   ROLES                  AGE   VERSION
master-node      Ready    control-plane,master   10m   v1.30.5
worker-node-1    Ready    <none>                 5m    v1.30.5
worker-node-2    Ready    <none>                 3m    v1.30.5

-----------------------------------------------------------------------------------------------------------------------------
Dashboard (https://kubernetes.io/fr/docs/tasks/access-application-cluster/web-ui-dashboard/)
-----------------------------------------------------------------------------------------------------------------------------
>  kubectl proxy
Starting to serve on 127.0.0.1:8001

> kubectl get svc -n kubernetes-dashboard
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.96.231.70    <none>        8000/TCP   13d
kubernetes-dashboard        ClusterIP   10.106.66.136   <none>        443/TCP    13d

> kubectl get secret -n kubernetes-dashboard
NAME                              TYPE     DATA   AGE
kubernetes-dashboard-certs        Opaque   0      13d
kubernetes-dashboard-csrf         Opaque   1      13d
kubernetes-dashboard-key-holder   Opaque   2      13d

https://github.com/Dadd76/Kubernetes/blob/main/example-voting-app/dashboard-admin.yaml
> kubectl apply -f D:\dashboard-admin.yaml
serviceaccount/admin-user created

>kubectl get serviceaccount admin-user -n kubernetes-dashboard
NAME         SECRETS   AGE
admin-user   0         62s
> kubectl get clusterrolebinding admin-user
NAME         ROLE                        AGE
admin-user   ClusterRole/cluster-admin   2m39s

Générer le token du nouvel admin (token valable 1 heure)
> kubectl create token admin-user -n kubernetes-dashboard
eyJhbGciOiJSUzI1NiIsImtpZCI6IlZOZnNxRWhwVWFLWVJwRno2c3lSdkJYbFNWN1NGWjRmLWZiN0JwWDliaW8ifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzQxNjI4Nzc0LCJpYXQiOjE3NDE2MjUxNzQsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwianRpIjoiMDU0OTUzZmItZWU5Yi00MTRiLWFhY2UtYjA5MzAyNDdhZDhmIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiNmM2YWM3NGYtMjQ3MC00Yzg4LThhNTItMzc1YWNiZDc1Mzc1In19LCJuYmYiOjE3NDE2MjUxNzQsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.K9or6zVApBxCTEBILgXUSpmIMPEfZS3vlG9kwYagGbV867lgvdg8lZliaAbxBXP0H5GuqmDkyVFdpjl5Ekf4n3Z3O3q43eokcdlplEs7ZRD2BrNszkwJ3peQqd13oq7pJzvHTUf6hS7hIYnwBBKubkMADvwapfYYVwRuimNvH08R06vepmV8Tt6Ld2bsrztLL0O3Mk5c9_aL90ZazRiLgd46DSkoln44vSkLjlTOYG--mTYbvdgSPZqNVSX4I6TArMuM5_DZijptTMtSxA4UCj88MGPTgn-hE0lZNl9F-2eBdLA3eWLsNw0VaIZcoBkQBe-YhgSMWezkg0mcsnaijw

>$tokenName = (kubectl get secret -n kubernetes-dashboard | Where-Object { $_ -match "admin-user-token" }) -split '\s+' | Select-Object -First 1
>kubectl get secret $tokenName -n kubernetes-dashboard -o jsonpath="{.data.token}" | ForEach-Object { [System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($_)) }

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/workloads?namespace=default

Installer Kubernetes Dashboard
Lance cette commande pour le déployer :
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
-----------------------------------------------------------------------------------------------------------------------------





