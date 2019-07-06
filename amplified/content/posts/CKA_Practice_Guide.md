+++
date = "2019-06-17"
title = "CKA Practice Quick guide"
slug = "CKA Practice Quick guide"
tags = [
    "CKA",
    "Kubernetes",
    "Kube"
]
categories = []
series = []
+++

## Chapter-1
- Installation (MAC)
1. Install kubectl
```
$curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
$chmod +x ./kubectl
$sudo mv ./kubectl /usr/local/bin/kubectl
```
2. Verify
```
$kubectl version
Client Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.2", GitCommit:"66049e3b21efe110454d67df4fa62b08ea79a19b", GitTreeState:"clean", BuildDate:"2019-05-16T16:23:09Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.11", GitCommit:"637c7e288581ee40ab4ca210618a89a555b6e7e9", GitTreeState:"clean", BuildDate:"2018-11-26T14:25:46Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
```
3. Enable kubectl autocompletion
```
$echo 'source <(kubectl completion bash)' >>~/.bashrc
$kubectl completion bash >/usr/local/etc/bash_completion.d/kubectl
```
4. Install minikube and start minikube
```
$curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube;
$minikube start
```

## Chapter-2
- Configure Pods and Clusters
- Enabling Metrics Server on Minikube
```yaml
$minikube addons enable metrics-server
$kubectl get apiservices
```
*Imperative command generator for easy kubectl* 
```yaml
POD :
kubectl run buysbox --image=busybox:latest --restart=Never --dry-run -o yaml > busybox.yaml
DEPLOYMENT:
kubectl run buysbox --image=busybox:latest  -o yaml --dry-run > busybox.yaml
JOB:
kubectl run buysbox --image=busybox:latest --restart=OnFailure  --dry-run -o yaml > busybox.yaml
```
Replace with appropriate shorthand generators.
* Deployment- Don’t specify the flag
* Pod— restart=Never
* Job— restart=OnFailure
* CronJob— restart=OnFailure — schedule=<some cron expression>
- Assign Memory and CPU Resources to Containers and Pods
```yaml
kubectl create namespace mem-example
kubectl run buysbox --image=busybox:latest --restart=Never --dry-run -o yaml > busybox.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: memdemo
  name: memdemo
spec:
  containers:
  - image: polinux/stress
    name: memdemo
    resources:
      limits:
         memory: "200Mi"
      requests:
         memory: "100Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: memdemo2
  name: memdemo2
spec:
  containers:
  - image: polinux/stress
    name: memdemo2
    resources:
      limits:
         memory: "1000Gi"
      requests:
         memory: "1000Gi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
  ```yaml
kubectl create namespace cpu-example
kubectl run buysbox --image=busybox:latest --restart=Never --dry-run -o yaml > busybox.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: cpu
  name: cpu
spec:
  containers:
  - image: vish/stress
    name: cpu
    resources:
      limits:
        cpu: "1"
      requests:
        cpu: "0.5"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
  ```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: cpu
  name: cpu
spec:
  containers:
  - image: vish/stress
    name: cpu
    resources:
      limits:
        cpu: "100"
      requests:
        cpu: "100"
    args:
    - -cpus
    - "2"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
#### QOS Examples 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: qos-demo
  namespace: qos-example
spec:
  containers:
  - name: qos-demo-ctr
    image: nginx
    resources:
      limits:
        memory: "200Mi"
        cpu: "700m"
      requests:
        memory: "200Mi"
        cpu: "700m"

apiVersion: v1
kind: Pod
metadata:
  name: qos-demo2
  namespace: qos-example
spec:
  containers:
  - name: qos-demo-ctr
    image: nginx
    resources:
      limits:
        memory: "200Mi"
        cpu: "700m"
      requests:
        memory: "200Mi"
        cpu: "700m"
apiVersion: v1
kind: Pod
metadata:
  name: qos-demo-4
  namespace: qos-example
spec:
  containers:

  - name: qos-demo-4-ctr-1
    image: nginx
    resources:
      requests:
        memory: "200Mi"

  - name: qos-demo-4-ctr-2
    image: redis
```

