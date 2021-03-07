# Kubernetes Setup
### Install Kind
see: https://kind.sigs.k8s.io/docs/user/quick-start/
* kind:
    ```
    curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
    chmod +x ./kind
    mv ./kind /usr/local/bin/
    ```

* kubectl:
    ```
    sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2 curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y kubectl
    ```
    
* docker: `apt install docker.io`

* docker permissions:
    ```
    sudo groupadd docker
    sudo usermod -aG docker ${USER}
    ```

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

* Ingress has link to issuer in
    ```
    metadata:
      annotations:
        cert-manager.io/cluster-issuer: "letsencrypt-prod"
    ```

* Ingress can now have several hosts specified to be covered by SSL under spec:
    ```
    spec:
      tls:
      - hosts:
          - kraeja.com
          - echo1.kraeja.com
          secretName: tls-secret
    ```

* For logs: `kubectl get events -n ingress-nginx --sort-by='.metadata.creationTimestamp'`
