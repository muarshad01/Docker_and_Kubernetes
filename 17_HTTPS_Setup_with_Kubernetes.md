## 293. HTTPS Setup Overview

* Setting up HTTPS requires a domain name purchase ($ 10 USD)

* Certificate Entity

***

## 294. Domain Purchase

* `domains.google.com` -> Search Domain (`k8s-multi.com`) -> Add to Cart ($12 / yr)
- Privacy protection is on
- Auto-renew is off


***

## 295. Domain Name Setup

* GCP Services -> Endpoint ->

***

## 296. Required Updates for Cert Manager Install

* Installing the `Cert Manager` using `Helm` on `GCP`.

***

## 297. Cert Manager Install

`https://github.com/cert-manager/cert-manager` -> Documentation -> `cert-manager.readthedocs.io` 
-> Getting Started - > Installation - > `Helm`

```
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.13.0 \
  # --set installCRDs=true
```
***

## 298. How to Wire Up Cert Manager



***

## 299. Required Update for Issuer

***

## 300. Issuer Config File

***

## 301. Required Update for Certificate

***

## 302. Certificate Config File

***

## 303. Deploying Changes

***

## 304. No Resources Found?

```
$ kubectl get certificates
```

***

## 305. Verifying the Certificate

***

## 306. Required Update for the HTTPS Ingress

***

## 307. `Ingress` Config for HTTPS


***

## 308. It Worked!


***

## 309. Google Cloud Cleanup


***

## 310. Local Environment Cleanup

```
$ kubectl delete -f k8s/
```

```
$ minikube stop
$ minikube start
$ minikube delete
```

```
$ docker ps
$ docker stop <container_id>
$ docker system prune
```

***
 
