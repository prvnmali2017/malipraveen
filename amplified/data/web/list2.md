### ServiceAccounts

- Create Service accounts
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-robot
EOF
``` 

- Create Secret for service accounts
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: build-robot-secret
  annotations:
    kubernetes.io/service-account.name: build-robot
type: kubernetes.io/service-account-token
EOF

```
- Create myregistry docker secret
- Add Image pullsecrets to service account

```
kubectl create secret docker-registry  myregistrykey --docker-server=https://index.docker.io/v1/ --docker-username=prvnmali17 --docker-password=Cl114897$@ --docker-email=praveen.mali@dubber.net

kubectl get secrets myregistrykey
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "myregistrykey"}]}'
kubectl get serviceaccounts default -o yaml > ./sa.yaml
```

```
apiVersion: v1
imagePullSecrets:
- name: myregistrykey
kind: ServiceAccount
metadata:
  creationTimestamp: "2019-07-03T11:21:45Z"
  name: default
  namespace: default
  selfLink: /api/v1/namespaces/default/serviceaccounts/default
  uid: bdcc216e-9d84-11e9-807f-080027b61050
secrets:
- name: default-token-2gfs5
imagePullSecrets:
- name: myregistrykey
```
```
kubectl replace serviceaccount default -f ./sa.yaml
```

- To test the service account 

```
kubectl run busybox --image=busybox:latest --restart=Never
```

    