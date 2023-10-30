# Workshop with Volume and Storage with Pods

## 1. [Host Path](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath)
Config minikube :: mount file from minikube with Host
* https://minikube.sigs.k8s.io/docs/handbook/mount/
```
$minikube mount $(pwd)/data:/data
```

Create Pods with Host Path
```
$kubectl apply -f host_path.yml

$kubectl get po
NAME                                              READY   STATUS    RESTARTS      AGE
test-pd                                           1/1     Running   0             6s
```

Expose port of Pods
```
$kubectl port-forward po/test-pd 8000:80
```
* Access to url = http://localhost:8000/

See and edit file from `$(pwd)/data`