# Kubernetes basic commands 


## Create `Hello world` pod
 
Create a basic pod 

```
apiVersion: v1
kind: Pod
metadata:
  name: hello
  labels:
    app: nextflow
spec:
  restartPolicy: Never
  containers:
  - name: busybox
    image: debian
    command: ["echo", "Hello world!"]
```


Save to a file named `hello.yml`. Then execute with the command: 

```
kubectl create -f hello.yml -o wide
```

## Basic management 

Check the status with: 

```
kubectl describe pods/hello
```

Get output with 

```
kubectl logs pods/hello
```


List pods 

```
kubectl get pods
```

List all pods 

```
kubectl get pods --show-all
```

List pods in all namespaces 

```
kubectl get pods --all-namespaces
```

## Create a persistent volume 

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gluster-pv   
spec:
  capacity:
    storage: 1Gi     
  accessModes:
  - ReadWriteMany    
  glusterfs:         
    endpoints: glusterfs-heketi
    path: /data 
    readOnly: false
  persistentVolumeReclaimPolicy: Retain 
```

Save to a file named `vol.yml`, then run: 

```
kubectl create -f vol.yml 
```

List the persistent volumes with: 

```
kubectl get pv
```

Create a persistent volume claim 

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gluster-claim  
spec:
  accessModes:
  - ReadWriteMany      
  resources:
     requests:
       storage: 1Gi   
```

Save to a file named `vol-claim.yml`, then run: 

```
kubectl create -f vol-claim.yml 
```   

List the persistent volume claims with: 

```
kubectl get pvc
```

Create a pod using the persistent volume: 

```
apiVersion: v1
kind: Pod
metadata:
  name: touch-1 
  labels:
    app: nextflow
spec:
  restartPolicy: Never
  containers:
  - name: busybox
    image: debian
    command: ["bash", "-c", "echo Hello world! > /data/hello.txt"]
    volumeMounts:
    - name: site-data
      mountPath: /data
  volumes:
  - name: site-data
    persistentVolumeClaim:
      claimName: gluster-claim 
```      

Save to a file named `pod-touch.yml`, then execute it with the command: 


```
kubectl create -f pod-touch.yml -o name 
```

Get the output with the command: 

```
kubectl logs pods/touch-1
```
 
 
 
## Useful links 

* [Gluster example](https://docs.openshift.org/latest/install_config/storage_examples/gluster_example.html) 
* [KinD](https://github.com/kubernetes-sigs/kind) - Kubernetes in Docker
