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

Import and Save the Dashboard.

![plot](./images/dashboard.png)


###
Changing the configmap of the 

```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
data:
  alertmanager.yml: |
    global:
      resolve_timeout: 1m
      slack_api_url: 'https://hooks.slack.com/services/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'
    route:
      group_by: ['alertname']
      group_wait: 10s
      group_interval: 10s
      repeat_interval: 1h
      receiver: 'slack-notifications'
    receivers:
    - name: 'slack-notifications'
      slack_configs:
      - channel: '#slack-notification-test'
        send_resolved: true
        icon_url: https://avatars3.githubusercontent.com/u/3380462
        icon_emoji: ':fire:'
        title: |-
          [{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }}] {{ .CommonLabels.alertname }} for {{ .CommonLabels.job }}
          {{- if gt (len .CommonLabels) (len .GroupLabels) -}}
            {{" "}}(
            {{- with .CommonLabels.Remove .GroupLabels.Names }}
              {{- range $index, $label := .SortedPairs -}}
                {{ if $index }}, {{ end }}
                {{- $label.Name }}="{{ $label.Value -}}"
              {{- end }}
            {{- end -}}
            )
          {{- end }}
        text: |-
          {{ range .Alerts -}} *Alert:* {{ .Annotations.title }}{{ if .Labels.severity }} - `{{ .Labels.severity }}`{{ end }}
          *Description:* {{ .Annotations.description }}
          *Details:*
            {{ range .Labels.SortedPairs }} • *{{ .Name }}:* `{{ .Value }}`
            {{ end }}
          {{ end }}
    templates:
    - /etc/alertmanager/*.tmpl
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"alertmanager.yml":"global:\n  resolve_timeout: 1m\n  api_url: 'https://hooks.slack.com/services/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'\nroute:\n  group_by: ['alertname']\n  group_wait: 10s\n  group_interval: 10s\n  repeat_interval: 1h\n  receiver: 'slack-notifications'\nreceivers:\n- name: 'slack-notifications'\n  slack_configs:\n  - channel: '#staging-pts'\n    send_resolved: true\n    icon_url: https://avatars3.githubusercontent.com/u/3380462\n    icon_emoji: ':fire:'\n    title: |-\n      [{{ .Status | toUpper }}{{ if eq .Status \"firing\" }}:{{ .Alerts.Firing | len }}{{ end }}] {{ .CommonLabels.alertname }} for {{ .CommonLabels.job }}\n      {{- if gt (len .CommonLabels) (len .GroupLabels) -}}\n        {{\" \"}}(\n        {{- with .CommonLabels.Remove .GroupLabels.Names }}\n          {{- range $index, $label := .SortedPairs -}}\n            {{ if $index }}, {{ end }}\n            {{- $label.Name }}=\"{{ $label.Value -}}\"\n          {{- end }}\n        {{- end -}}\n        )\n      {{- end }}\n    text: |-\n      {{ range .Alerts -}} *Alert:* {{ .Annotations.title }}{{ if .Labels.severity }} - `{{ .Labels.severity }}`{{ end }}\n      *Description:* {{ .Annotations.description }}\n      *Details:*\n        {{ range .Labels.SortedPairs }} • *{{ .Name }}:* `{{ .Value }}`\n        {{ end }}\n      {{ end }}\ntemplates:\n- /etc/alertmanager/*.tmpl\n"},"kind":"ConfigMap","metadata":{"annotations":{"meta.helm.sh/release-name":"prometheus","meta.helm.sh/release-namespace":"default"},"creationTimestamp":"2023-08-08T05:18:06Z","labels":{"app.kubernetes.io/instance":"prometheus","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"alertmanager","app.kubernetes.io/version":"v0.25.0","helm.sh/chart":"alertmanager-0.33.1"},"name":"prometheus-alertmanager","namespace":"default","resourceVersion":"42553","uid":"aa9a294f-e9fd-44fa-bd3a-2b541cd2a9cc"}}
    meta.helm.sh/release-name: prometheus
    meta.helm.sh/release-namespace: default
  creationTimestamp: "2023-08-08T05:18:06Z"
  labels:
    app.kubernetes.io/instance: prometheus
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: alertmanager
    app.kubernetes.io/version: v0.25.0
    helm.sh/chart: alertmanager-0.33.1
  name: prometheus-alertmanager
  namespace: default
  resourceVersion: "42867"
  uid: aa9a294f-e9fd-44fa-bd3a-2b541cd2a9cc
```


