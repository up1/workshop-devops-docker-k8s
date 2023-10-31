# HPA with custom metric

## Step 1 :: Disable Metric Server
```
$minikube addons disable metrics-server
```

## Step 2 :: Install kube-prometheus
```
$git clone https://github.com/coreos/kube-prometheus
$kubectl apply -f kube-prometheus/manifests/setup
$kubectl apply -f kube-prometheus/manifests/

$kubectl get all --namespace monitoring
$kubectl get svc --namespace monitoring

$kubectl get po --namespace monitoring
$kubectl get po --namespace monitoring
NAME                                   READY   STATUS    RESTARTS   AGE
alertmanager-main-0                    2/2     Running   0          2m31s
alertmanager-main-1                    2/2     Running   0          2m31s
alertmanager-main-2                    2/2     Running   0          2m31s
blackbox-exporter-5fd45d4b87-ndqqc     3/3     Running   0          2m33s
grafana-6ccd547d9-gchjv                1/1     Running   0          2m33s
kube-state-metrics-6698b96964-gjr5r    3/3     Running   0          2m32s
node-exporter-qsxbf                    2/2     Running   0          2m32s
prometheus-adapter-7cd4b79b8c-bn8m8    1/1     Running   0          2m32s
prometheus-adapter-7cd4b79b8c-rwwww    1/1     Running   0          2m32s
prometheus-k8s-0                       2/2     Running   0          2m30s
prometheus-k8s-1                       2/2     Running   0          2m30s
prometheus-operator-866c5bcb8c-x48nd   2/2     Running   0          2m32s
```

Access to Prometheus
* http://localhost:9090
```
$kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
```

Access to Grafana
* http://localhost:3000
```
$kubectl --namespace monitoring port-forward svc/grafana 3000
```

Access to Alert Manager
* http://localhost:9030
```
$kubectl --namespace monitoring port-forward svc/alertmanager-main 9093
```

## Step 3 :: Deploy with custom metrics

```
$kubectl apply -f deployment.yml
$kubectl apply -f service.yml

$kubectl get po

$kubectl get service
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hpa2-service          NodePort    10.107.141.21    <none>        80:30553/TCP     82s
```

```
$kubectl port-forward svc/metrics-example 8000:80
```
Acccess to service
* http://127.0.0.1:8000
* http://127.0.0.1:8000/metrics

### Apply custom metrics collector
```
$kubectl apply -f sm.yml
```

## 4. Create Horizontal Pods Autoscale (HPA)
```
$kubectl apply -f hpa.yml

$kubectl describe hpa
$kubectl get hpa
NAME    REFERENCE                    TARGETS        MINPODS   MAXPODS   REPLICAS   AGE
hpav2   Deployment/metrics-example   <unknown>/20   2         10        2          63s
```

## 5. Load Testing
```
$kubectl run --rm=true -i --tty load-test --image=busybox /bin/sh
>wget -q -O- http://metrics-example.default.svc.cluster.local
>while true; do wget -q -O- http://metrics-example.default.svc.cluster.local; done
```

Watch HPA
```
$kubectl get hpa hpav2 -w
```

Watch ReplicatSet
```
$kubectl get rs -w
```

## 6. Delete all resources
```
$kubectl delete -f deployment.yml
$kubectl delete -f service.yml
$kubectl delete -f sm.yml
$kubectl delete -f hpa.yml

$kubectl delete all --all
```