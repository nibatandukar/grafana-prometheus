#Install Prometheus

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install <name> prometheus-community/prometheus

```
Check the status of the pods.

Check the promethues server by doing the port forward

```
kubectl port-forward svc/prometheus-server 8090:80 --address 0.0.0.0
```

## Install the grafana 


```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana
```

Get the password of the gafana 
###
```
kubectl get secret — namespace default grafana -o yaml

echo “password_value” | openssl base64 -d ; echo

echo “username_value” | openssl base64 -d ; echo
```
###
