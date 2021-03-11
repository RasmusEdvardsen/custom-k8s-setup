# Dashboard

## Steps

* Add service account and bind account to cluster role: `kubectl apply -f ./`

* Either
  - run: `kubectl -n kubernetes-dashboard get secret admin-user-token-hrq8h -o yaml` and then base64decode token
  - or run:
  `kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"`

* `kubectl proxy` and access dashboard on: 
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

* https://github.com/kubernetes/dashboard