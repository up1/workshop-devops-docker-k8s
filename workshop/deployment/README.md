# Deployment Strategies

## 1. Recreate

Deploy first version
```
$kubectl apply -f recreate.yml
$kubectl get rs -w

$kubectl port-forward deployment/nginx 8000:80
```

Deploy with new version
```
$kubectl set image deployment/nginx nginx=nginx:1.25.3
$kubectl get rs -w
```

Delete all resources
```
$kubectl delete -f recreate.yml
```

## 2. RollingUpdate

Deploy first version
```
$kubectl apply -f rollingupdate.yml
$kubectl get rs -w
```

Deploy with new version
```
$kubectl set image deployment/nginx nginx=nginx:1.25.3
$kubectl get rs -w

NAME               DESIRED   CURRENT   READY   AGE
nginx-695bb6dbb6   5         5         5       110s
nginx-7b4d6c5684   0         0         0       2m29s
```

Delete all resources
```
$kubectl delete -f rollingupdate.yml
```


## 3. Ramped Slow Rollout

Deploy first version
```
$kubectl apply -f rollingupdate-slow.yml
$kubectl get rs -w
```

Deploy with new version
```
$kubectl set image deployment/nginx nginx=nginx:1.25.3
$kubectl get rs -w
```

Delete all resources
```
$kubectl delete -f rollingupdate-slow.yml
```

## 4. Blue-Green or Canary Deployment 

Deploy first version
```
$kubectl apply -f blue-green/blue.yml
$kubectl get rs -w
NAME                     DESIRED   CURRENT   READY   AGE
nginx-blue-7b4d6c5684    5         5         5       2m19s
```

Deploy service
```
$kubectl apply -f blue-green/service_blue.yml
$kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx        ClusterIP   10.109.191.49   <none>        80/TCP    4s

$kubectl port-forward service/nginx 8000:80
```

Testing
```
$curl -sS -D -  http://localhost:8000/ | grep "Server:"
Server: nginx/1.24.0
```

Deploy with new version
```
$kubectl apply -f blue-green/green.yml
$kubectl get rs -w
NAME                     DESIRED   CURRENT   READY   AGE
nginx-blue-7b4d6c5684    5         5         5       2m19s
nginx-green-695bb6dbb6   5         5         5       15s

$kubectl apply -f blue-green/service_green.yml
```

Start Kubernetes dashboard with minikube
```
$minikube dashboard --url
```

Scaling down first version (Blue)
```
$kubectl scale deployment nginx-blue --replicas=0
$kubectl delete -f blue-green/service_blue.yml
$kubectl delete -f blue-green/blue.yml
```

Testing
```
$curl -sS -D -  http://localhost:8000/ | grep "Server:"
Server: nginx/1.25.3
```

Delete all resources
```
$kubectl delete -f blue-green/blue.yml
$kubectl delete -f blue-green/green.yml
$kubectl delete -f blue-green/service_blue.yml
$kubectl delete -f blue-green/service_green.yml
```