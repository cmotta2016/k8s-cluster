## Install Elasticsearch
$ helm install elastic/elasticsearch --name elasticsearch --namespace kube-logging --version 7.3.0 --set imageTag=7.3.0 --set esJavaOpts="-Xmx512m -Xms512m" --set service.type=LoadBalancer

## Check progress
$ kubectl get pods --namespace=kube-logging -l app=elasticsearch-master -w

## Install Kibana
$ helm install elastic/kibana --name kibana --namespace kube-logging --version 7.3.0 --set imageTag=7.3.0 --set service.type=LoadBalancer

## Check progress
$ kubectl get pods --namespace=kube-logging -l app=kibana -w  

## Install Fluentd
$ helm install kiwigrid/fluentd-elasticsearch --name fluentd --namespace kube-logging --version 5.1.0 --set elasticsearch.host=elasticsearch-master

## Edit DaemonSet and add the following lines if your kubernetes cluster is running on OCI
$ kubectl edit ds fluentd-fluentd-elasticsearch -n kube-logging
```
....
        volumeMounts:
        - mountPath: /u01
          name: u01
          readOnly: true
...
      volumes:
      - hostPath:
          path: /u01
          type: ""
```
