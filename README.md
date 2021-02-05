# Kubernetes

### Note
* Seems port forwarding of 80 and 443 was what I missed - very important: 
https://kind.sigs.k8s.io/docs/user/configuration/#extra-port-mappings

## Steps

### Install Kind
see: https://kind.sigs.k8s.io/docs/user/quick-start/
* Run:
    ```
    curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.10.0/kind-linux-amd64
    chmod +x ./kind
    mv ./kind /some-dir-in-your-PATH/kind
    ```

### Quickstart
* `kind create cluster --config cluster_init/cluster.yml `
* `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml`
* `kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.16.1/cert-manager.yaml`
* After this step, wait a bit for the cert-manager and ingress-nginx objects to be created.
* `kubectl apply -f quickstart/`

#### Notes
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
* `kubectl apply -f ingress.yml`