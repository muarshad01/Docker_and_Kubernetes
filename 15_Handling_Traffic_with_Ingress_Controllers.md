## 236. Load Balancer Services

`Ingress`: Exposes a set of services to the outside world

***

## 237. A Quick Node on Ingress

* We're using **ingress-nginx**
    - `github.com/kubernetes/ingress-nginx`

* We're not using **kubernetes-ingress**
    - `github.com/nginxinc/kubernetes-ingress`

***

## 238. One Other Quick Note

***

## 239. Behind the Scenes of Ingress

* `Ingress Controller`
* `Ingress Config`

***

## 240. More Behind the Scenes of Ingress

* Sticky Sessions

***

## 241. Optional Reading on Ingress Nginx

***

## 242. Docker Driver and Ingress- IMPORTANT

Delete your cluster:
```
$ minikube delete
```

Restart with a different driver, macOS:
```
$ minikube start --driver=hyperkit
```

***

## 243. Important - DO NOT SKIP - Ingress Nginx Installation Info

```
$ minikube addons enable ingress
```

`https://kubernetes.github.io/ingress-nginx/deploy/#minikube`

***

## 244. Setting up Ingress Locally with Minikube

`https://kubernetes.github.io/ingress-nginx/deploy/#minikube` -> Deployment -> Installation Guide ...??

```
$ minikube addons enable ingress
```

***

## 245. Ingress v1 API Update+

***

## 246. Creating the Ingress Configuration



***

## 247. Testing Ingress Locally

***

## 248. The Minikube Dashboard

***

## 249. Docker Desktop's Kubernetes Dashboard

***
