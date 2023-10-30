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


Delete all resources
```
$kubectl delete -f host_path.yml
```

## 2. [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
* [Configure a Pod to Use a PersistentVolume for Storage](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage)

### 2.1 Create a Persistent Volume (PV)


```
// Start mount file
$minikube mount $(pwd)/data:/data

// Create PV
$kubectl apply -f pv/pv.yml

$kubectl get pv
NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
task-pv-volume   10Gi       RWO            Retain           Available           manual                  5s
```

### 2.2 Create a PersistentVolumeClaim (PVC)

```
// Create PV
$kubectl apply -f pv/pvc.yml

$kubectl get pvc
NAME            STATUS   VOLUME           CAPACITY   ACCESS MODES   STORAGECLASS   AGE
task-pv-claim   Bound    task-pv-volume   10Gi       RWO            manual         4s
```

### 2.3 Create a Pods

```
// Create Pods
$kubectl apply -f pv/pod.yml

$kubectl get po
NAME                                              READY   STATUS    RESTARTS      AGE
task-pv-pod                                       1/1     Running   0             11s
```

Expose port of Pods
```
$kubectl port-forward po/task-pv-pod 8000:80
```
* Access to url = http://localhost:8000/

Delete all resources
```
$kubectl delete -f pod.yml
$kubectl delete -f pvc.yml
$kubectl delete -f pv.yml
```