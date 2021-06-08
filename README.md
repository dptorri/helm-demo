# Helm Demo

This repository showcase how Helm can simplify deployment process of k8s cluster

## Part 1
🌎 Internet 192.168.99.100

⬇️

🚪 Ingress (Nginx-ingress) Port:443

⬇️ 

🚚  Nginx-service Port:80 (Selector -> app:nginx)

⬇️ 

🖥️  3 Pods (app:nginx port:80)

Creates the helm chart with default values and increase pods to 3

 `helm create part1`

 Add 3 Replicas in values.yml

`replicaCount: 3`

Deploy

`helm install miami-heat ./part1`

Check running pods

`k get pods`
```
NAME                                            READY   STATUS    RESTARTS   AGE
miami-heat-part1-9788fdf48-hz8ph                1/1     Running   0          22s
miami-heat-part1-9788fdf48-kh8m7                1/1     Running   0          22s
miami-heat-part1-9788fdf48-m5pz4                1/1     Running   0          22s
```
`k get deployments`
```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
miami-heat-part1   3/3     3            3           4m45s
```
`helm uninstall miami-heat`
```
miami-heat uninstalled
```
## Part 2
Create a local registry using [kind](https://kind.sigs.k8s.io/docs/user/local-registry/)


```
brew install kind
```
```
chmod u+x kind-with-registry.sh
```
```
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.21.1) 🖼 
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Have a nice day! 👋
configmap/local-registry-hosting created
```
The registry can be used like this:

- First we'll pull an image 
```
docker pull gcr.io/google-samples/hello-app:1.0
```
- Then we'll tag the image to use the local registry 
```
docker tag gcr.io/google-samples/hello-app:1.0 localhost:5000/hello-app:1.0
```
- Then we'll push it to the registry 
```
docker push localhost:5000/hello-app:1.0
```
- And now we can use the image 
```
kubectl create deployment hello-server --image=localhost:5000/hello-app:1.0
```


## Part 3
### Setup an Ingress Controller
`helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`
````
"ingress-nginx" has been added to your repositories
````
`helm repo update`
````
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
````
`helm install miami-heat ingress-nginx/ingress-nginx`
```
NAME: miami-heat
LAST DEPLOYED: Tue Jun  8 13:28:44 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
```
