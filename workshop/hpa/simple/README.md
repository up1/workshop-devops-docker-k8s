# HPA :: Horizontal Pods Autoscaler
* Default with CPU
* Custom metrics 

## 1. Working with minikube :: enable metric server
```
$minikube addons enable metrics-server
$minikube addons list
```

## 2. Deployment Golang project
```
$kubectl apply -f simple/deployment.yml

$kubectl describe po
$kubectl get po
NAME                                              READY   STATUS    RESTARTS       AGE
hpav1-web-659d8fdc8c-zmbhl                        1/1     Running   0              17s

$kubectl get rs
NAME                                        DESIRED   CURRENT   READY   AGE
hpav1-web-659d8fdc8c                        1         1         1       46s

$kubectl apply -f simple/service.yml
$kubectl get service

// Open Dashboard
$minikube dashboard --url
```

## 3. Testing
```
$minikube service hpa-service --url

$kubectl run --rm=true -i --tty load-test --image=busybox /bin/sh
>wget -q -O- http://hpa-service.default.svc.cluster.local
```

## 4. Create Horizontal Pods Autoscale (HPA)
```
$kubectl apply -f simple/hpa.yml

$kubectl describe hpa
$kubectl get hpa
NAME    REFERENCE              TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
hpav1   Deployment/hpav1-web   <unknown>/10%   2         10        0          12s
```

## 5. Load Testing
```
$kubectl run --rm=true -i --tty load-test --image=busybox /bin/sh
>wget -q -O- http://hpa-service.default.svc.cluster.local
>while true; sleep 0.001s; do wget -q -O- http://hpa-service.default.svc.cluster.local; done
```

Watch HPA
```
$kubectl get hpa hpav1 -w
```

Watch ReplicatSet
```
$kubectl get rs -w
```

## 6. Delete all resources
```
$kubectl delete -f simple/deployment.yml
$kubectl delete -f simple/service.yml
$kubectl delete -f simple/hpa.yml
```



