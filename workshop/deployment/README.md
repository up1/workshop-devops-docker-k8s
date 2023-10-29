# Deployment Strategies

## 1. Recreate

Deploy first version
```
$kubectl apply -f recreate.yml
$kubectl get rs -w
```

Deploy with new version
```
$kubectl set image deployment/nginx nginx=nginx:1.25.3
$kubectl get rs -w
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

## 4. Blue-Green Deployment
