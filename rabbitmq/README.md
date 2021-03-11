# RabbitMQ
Stuff

## Get creds
```
username="$(kubectl get secret rabbit-mq-cluster-default-user -o jsonpath='{.data.username}' | base64 --decode)"
# echo "username: $username"
password="$(kubectl get secret rabbit-mq-cluster-default-user -o jsonpath='{.data.password}' | base64 --decode)"
# echo "password: $password"
service="$(kubectl get service rabbit-mq-cluster -o jsonpath='{.spec.clusterIP}')"
kubectl run perf-test --image=pivotalrabbitmq/perf-test -- --uri amqp://$username:$password@$service
kubectl logs --follow perf-test
# admin.mq.kraeja.com to see plots, graphs
```