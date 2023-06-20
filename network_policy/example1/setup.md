# Example 1 
[<img src="https://github.com/zuza3012/k8s_examples/blob/main/network_policy/example1/example1.svg" width="800"/>](example1.svg)
## Usage
* start minikube with network policy
```
minikube start --cni calico
```

* create three namespaces: frontend, backend, admin; run in each namespace pod: web,api, admin and expose those pods (ClusterIP service) . Use image nginx, open ports 80 add labels like app=web, app=api ...:


```
kubectl create ns frontend
kubectl label namespaces frontend role=frontend
kubectl run  web --image=nginx --labels=app=web --port 80 -n frontend
kubectl expose pod web --type=ClusterIP --port=80 -n frontend
```
```
kubectl create ns backend
kubectl label namespaces backend role=backend
kubectl run api --image=nginx --labels=app=api --port 80 -n backend
kubectl expose pod api --type=ClusterIP --port=80 -n backend
```
```
kubectl create ns admin
kubectl label namespaces admin role=admin
kubectl run admin --image=nginx --labels=app=admin --port 80 -n admin
kubectl expose pod admin --type=ClusterIP --port=80 -n admin
```
No networkpolicy added to any namespace so you can do: 
```
k exec web -it --tty -n frontend -- curl admin.admin
``` 

and

``` 
k exec api -it --tty -n backend -- curl admin.admin
```

Both should return something like this:
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
...
```

Now add resouce admin-allow.yaml :
```
k apply -f admin-allow-web.yaml
```

Doing this ```k exec api -it --tty -n backend -- curl admin.admin``` will hang terminal, but you can success with command:
```
k exec web -it --tty -n frontend -- curl admin.admin
```
