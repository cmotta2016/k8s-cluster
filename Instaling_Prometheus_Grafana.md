## Install Prometheus
	$ helm install stable/prometheus --namespace kube-monitoring --name prometheus --set server.service.type=LoadBalancer --set alertmanager.service.type=LoadBalancer

### Check progress
	$ kubectl --namespace=kube-monitoring get pods -l app=prometheus -w

## Instaling Grafana
### Create configmap
	$vim config.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-grafana-datasource
  namespace: kube-monitoring
  labels:
    grafana_datasource: '1'
data:
  datasource.yaml: |-
    apiVersion: 1
    datasources:
    - name: Prometheus
      type: prometheus
      access: proxy
      orgId: 1
      url: http://prometheus-server.kube-monitoring.svc.cluster.local # Need to match your prometheus service endpoint
```
### Applying configmap
	$ kubectl apply -f config.yaml
	$ kubectl get configmaps -n kube-monitoring

### Create values file
	$ vim values.yml
```
sidecar:
  datasources:
    enabled: true
    label: grafana_datasource
```

## Install Grafana
	$ helm install --name grafana --namespace kube-monitoring stable/grafana --set service.type=LoadBalancer -f values.yml

### Get password for "admin" user
	$ kubectl get secret --namespace kube-monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


