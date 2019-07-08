	Configure a Pod to Use a Volume for Storage
	
	
	```
	apiVersion: v1
	kind: Pod
	metadata:
	  name: redis
	spec:
	  containers:
	  - name: redis
	    image: redis
	    volumeMounts:
	    - name: redis-storage
	      mountPath: /data/redis
	  volumes:
	  - name: redis-storage
	    emptyDir: {}
	```
	
	
	
Configure the Pod to use Persistence Storage

```
kind: PersistentVolume
apiVersion: v1
metadata:
  name: task-pv-volume
  annotations:
    pv.beta.kubernetes.io/gid: "20"
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"	
```

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
```
kind: Pod
apiVersion: v1
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
```
Configure a Pod to Use a Projected Volume for Storage
- secrets
- configmap
- downward API
- service accounts

#### Create files containing the username and password:
echo -n "admin" > ./username.txt
echo -n "1f2d1e2e67df" > ./password.txt

#### Package these files into secrets:
kubectl create secret generic user --from-file=./username.txt
kubectl create secret generic pass --from-file=./password.txt

```
apiVersion: v1
kind: Pod
metadata:
  name: test-projected-volume
spec:
  containers:
  - name: test-projected-volume
    image: busybox
    args:
    - sleep
    - "86400"
    volumeMounts:
    - name: all-in-one
      mountPath: "/projected-volume"
      readOnly: true
  volumes:
  - name: all-in-one
    projected:
      sources:
      - secret:
          name: user
      - secret:
          name: pass
```
