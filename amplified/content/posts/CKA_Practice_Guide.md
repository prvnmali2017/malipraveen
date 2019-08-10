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
```
$minikube addons enable metrics-server
$kubectl get apiservices
```
*Imperative command generator for easy kubectl* 
```
POD :
kubectl run buysbox --image=busybox:latest --restart=Never --dry-run -o  > busybox.
DEPLOYMENT:
kubectl run buysbox --image=busybox:latest  -o  --dry-run > busybox.
JOB:
kubectl run buysbox --image=busybox:latest --restart=OnFailure  --dry-run -o  > busybox.
```
Replace with appropriate shorthand generators.
* Deployment- Don’t specify the flag
* Pod— restart=Never
* Job— restart=OnFailure
* CronJob— restart=OnFailure — schedule=<some cron expression>
- Assign Memory and CPU Resources to Containers and Pods
```
kubectl create namespace mem-example
kubectl run buysbox --image=busybox:latest --restart=Never --dry-run -o  > busybox.
```
```
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
```
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
  ```
kubectl create namespace cpu-example
kubectl run buysbox --image=busybox:latest --restart=Never --dry-run -o  > busybox.
```
```
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
  ```
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
```
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

#### Tips

Certification Tips - Imperative Commands with Kubectl
While you would be working mostly the declarative way - using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save considerable amount of time during your exams.

Before we begin, familiarize with the two options that can come in handy while working with the below commands:

--dry-run: By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the --dry-run option. This will not create the resource, instead, tell you weather the resource can be created and if your command is right.

-o yaml: This will output the resource definition in YAML format on screen.



Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.



POD
Create an NGINX Pod
```
kubectl run --generator=run-pod/v1 nginx --image=nginx
```


Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml
```


Deployment
Create a deployment
```
kubectl run --generator=deployment/v1beta1 nginx --image=nginx
```
Or the newer recommended way:
```
kubectl create deployment --image=nginx nginx
```


Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run -o yaml
```
Or
```
kubectl create deployment --image=nginx nginx --dry-run -o yaml
```


Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
```
kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml

kubectl create deployment does not have a --replicas option. You could first create it and then scale it using the kubectl scale command.
```


Save it to a file - (If you need to modify or add some other details)
```
kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml
```


Service
Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
```
kubectl expose pod redis --port=6379 --name redis-service --dry-run -o yaml
```
(This will automatically use the pod's labels as selectors)

Or
```
kubectl create service clusterip redis --tcp=6379:6379 --dry-run -o yaml  
```
(This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)



Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
```
kubectl expose pod nginx --port=80 --name nginx-service --dry-run -o yaml
```
(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or
```
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml
```   
(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

'.items.status.images'


<div align=center><a href='https://www.counter12.com'><img src='https://www.counter12.com/img-xAzZ1zDDd4ZzBwZZ-50.gif' border='0' alt='contador de acesso grátis'></a><script type='text/javascript' src='https://www.counter12.com/ad.js?id=xAzZ1zDDd4ZzBwZZ'></script></div>
