# Kubernetes Setup
### Prereqs
see: https://kind.sigs.k8s.io/docs/user/quick-start/
* DNS domain name
* kind
* kubectl    
* docker

___
___

## Setup k8s cluster
* `kind create cluster --config cluster.yml`
* `kind get kubeconfig > ~/.kube/config`

## Apply external configs
* `kubectl apply -f ext/ingress-nginx.yml`
* `kubectl apply --validate=false -f ext/cert-manager.yml`
* After this step, wait a bit for the cert-manager and ingress-nginx objects to be created.

## Private image registry
Create a personal access token from github, and use as follows: \
`docker login ghcr.io --username RasmusEdvardsen --password PERSONAL_ACCESS_TOKEN` \
This will output a path where docker saved the config. Use the path as follows: \
`kubectl create secret generic ghcrcreds --from-file=.dockerconfigjson=/PATH/TO/CONFIG.JSON --type kubernetes.io/dockerconfigjson`

This secret can now be used for pods/deployments, as follows:
```
spec:
  imagePullSecrets:
  - name: ghcrcreds
```

### Important note about image registries:
docker.pkg.github.com is not supported well. \
ghcr.io is the new and improved github image registry

## Setup ssl issuer
* `kubectl apply -f cluster-issuer.yml`

## Deploy services to k8s
We can now deploy services:
* `kubectl apply -f services/`
* `kubectl apply -f ingress.yml`

___
___

### Notes
* The `cluster.yml` config file contains most importantly extra port mappings from the kind cluster to the host network on port 80 and 443.

* For more about Ingress: \
    https://kind.sigs.k8s.io/docs/user/ingress/

* For more about CertManager: \
    https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes

* Loading local docker images in to kind k8s: `kind load docker-image my-custom-image --name kind-2`

* For logs: `kubectl get events -n ingress-nginx --sort-by='.metadata.creationTimestamp'`

* For object-detection.kraeja.com to work, need to add model.h5 to /mnt/k8s/models/