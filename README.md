## Install Prometheus
 
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

Check the dashboard by changing the ports

```
kubectl port-forward svc/grafana 8090:80 --address 0.0.0.0

```

Add the prometheus in the gfrafana dashboard. Addthe service name and the service port.

```
Kubectl get svc
prometheus-server                     ClusterIP   10.43.211.117   <none>        80/TCP 
```

![plot](./images/dashboar-cluster-IP.png)

### Add one dashboard
Add the dashboard and copy the ID 12740
https://grafana.com/grafana/dashboards/12740-kubernetes-monitoring/

![plot](./images/dashboard1.png)



![plot](./images/dashboard.png)





