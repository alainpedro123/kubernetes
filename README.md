Kubernetes Fundamentals 💻🚀
===============================

Getting started with Kubernetes - Exercises & Solutions

a) Getting started
----
**1. What is the version of Kubernetes running on the nodes?**
```sh
$ kubectl version
```

b) PODs with YAML
----
**1. Create a new pod with the nginx image.**
```sh
$ kubectl run newpod --image=nginx
```

**2. List the all PODs that have been created.**
```sh
$ kubectl get pods
$ kubectl get pods -o wide
```

**3. What is the image used to create the new pods?**
```sh
$ kubectl describe pod nginx
$ kubectl describe pod pod_name-<id> | grep -i image
```

**4. Which nodes are these pods placed on?**
```sh
kubectl get nodes -o wide
```

**5. How many containers are part of the pod webapp?**
```sh
kubectl describe pod webapp
```

**6. What images are used in the new webapp pod?**
```sh
$ kubectl describe pod webapp 
```

**7. What is the state of the container agentx in the pod webapp?**
```sh
$ kubectl describe pod webapp
```

**8. Why do you think the container agentx in pod webapp is in error?**
```sh
$ kubectl describe pod webapp
```

**9. What does the READY column in the output of the "kubectl get pods" command indicate?**
> Running container in the POD / Total containers in POD

**10. Delete the webapp Pod. Once deleted, wait for the pod to fully terminate.**
```sh
$ kubectl delete pod webapp
```

**5. Create a POD using YAML based configuration file (pod.yml)**
```sh
$ vim pod.yml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
  	tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx
```   

```sh
$ "kubectl apply -f pod.yaml" OR "kubectl create -f pod.yaml"
$ kubectl get pods
$ kubectl describe pod nginx
```

**11. Create a new pod with the name redis and with the image redis123. Use a pod-definition YAML file. (the image name is wrong)**
```sh
$ kubectl run redis --image=redis123 --dry-run=client -o yaml > redis-definition.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis123
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```sh
$ kubectl apply -f redis-definition.yaml
```

x) Imperative commands
---
**1. Deploy a pod named nginx-pod using the nginx:alpine image.**
```ssh
$ kubectl run nginx-pod --image=nginx:alpine
```

**2. Deploy a redis pod using the redis:alpine image with the labels set to tier=db.**
```ssh
$ kubectl run redis --image=redis:alpine --labels=tier=db
```

**3. Create a service redis-service to expose the redis application within the cluster on port 6379.**
* Service: redis-service
* Port: 6379
* Type: ClusterIP

```ssh
$ kubectl expose pod redis --port=6379 --name redis-service
```

**4. Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.**
```ssh
$ kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3
```

**5. Create a new pod called custom-nginx using the nginx image and expose it on container port 8080.**
```ssh
$ kubectl run custom-nginx --image=nginx --port=8080
```

**6. Create a new namespace called dev-ns.**
```ssh
$ kubectl create namespace dev-ns
$ kubectl create ns dev-ns
```

**7. Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.**
```ssh
$ kubectl create deployment redis-deploy -n dev-ns --image=redis --replicas=2
```

**8. Create a pod called "httpd" using the image "httpd:alpine" in the default namespace. Next, create a service of type "ClusterIP" by the same name (httpd). The target port for the service should be 80.**
```ssh
$ kubectl run httpd --image=httpd:alpine --port=80 --expose
```


c) Replication Controller / ReplicaSets
----
**1. Create "Replication Controller" using YAML based configuration file (rc-definition.yml)**
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
  	app: myapp
  	type: frontend

spec:
  template:

  	metadata:
  	  name: myapp-pod
  	  labels:
  	  	app: myapp
  	  	type: front-end
  	spec:
  	  containers:
  	  -  name: nginx-container
  	     image: nginx

  replicas: 3
```

```sh
$ kubectl create -f rc-definition.yml     # create the replication controller
$ kubectl get replicationcontroller       # get the list of created replication controller
```


**2. How many ReplicaSets exist on the system?**
```sh
$ kubectl get replicaset
```

**3. How about now? How many ReplicaSets do you see?**
```sh
$ kubectl get replicaset
```

**4. How many PODs are DESIRED in the new-replica-set?**
```sh
$ kubectl get replicaset
```

**5. Create "Replica Set" using YAML based configuration file (rc-definition.yml)**
```yaml
apiVersion: apps/v1
kind: ReplicationSet
metadata:
  name: myapp-replicaset
  labels:
  	app: myapp
  	type: frontend

spec:
  template:

  	metadata:
  	  name: myapp-pod
  	  labels:
  	  	app: myapp
  	  	type: front-end
  	spec:
  	  containers:
  	  -  name: nginx-container
  	     image: nginx

  replicas: 3
  selector:
  	matchLabels:
  		type: front-end
```
```sh
$ kubectl create -f replicaset-definition.yml       # create the replication controller
$ kubectl get replicaset                            # get the list of created replicaset
```

**6. Update the replica to scale from 3 to 6**

Change the YAML file and then run
```sh
$kubectl replace -f replicaset-definition.yml."
```

Alternatively you could do the update by running one of the commands below:
```sh
$ kubectl scale -–replicas=6 –f replicaset-definition.yml
$ kubectl scale -–replicas=6 replicaset myapp-replicaset
$ kubectl edit replicaset myapp-replicaset
$ kubectl scale replicaset myapp-replicaset --replicas=6
```

**7. What is the image used to create the pods in the new-replica-set?**
```sh
$ kubectl describe replicaset new-replica-set
```

**8. How many PODs are READY in the new-replica-set?**
```sh
$ kubectl describe replicaset new-replica-set
```

**9. Why "new-replica-set" is not on READY status?**
```sh
$ kubectl get pods
```
*Status: ImagePullBackOff = The image "busybox777" does not exist.*

**10. Delete any of the 4 existing PODs**
```sh
$ kubectl delete pod POD-NAME
```

**11. How many PODS exist now?**
```sh
$ kubectl get pods
```

**12. Why are there still 4 PODs, even after you deleted one?**
```
ReplicaSet ensures that desired number of PODs always run
```

**13. Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root/. There is an issue with the file, so try to fix it.**
```sh
$ kubectl create -f replicaset-definition-1.yaml
```

**14. Create a ReplicaSet using the replicaset-definition-2.yaml file located at /root/. There is an issue with the file, so try to fix it.**
```sh
$ kubectl create -f replicaset-definition-2.yaml
```

**15. Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2**
```sh
$ kubectl delete replicaset replicaset-1
$ kubectl delete replicaset replicaset-2
```

**16. Fix the original replica set new-replica-set to use the correct busybox image. Either delete and recreate the ReplicaSet or Update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.**
```sh
$ kubectl edit replicaset new-replica-set
$ kubectl delete --all pods
```

**17. Scale the ReplicaSet to 5 PODs. Use kubectl scale command or edit the replicaset using kubectl edit replicaset.**
```sh
$ kubectl scale --replicas=5 replicaset new-replica-set
```

**18. Now scale the ReplicaSet down to 2 PODs.**
```sh
$ kubectl scale --replicas=2 replicaset new-replica-set
```


d) Services
----
**1. How many Services exist on the system?**
```sh
$ kubectl get svc
```

**2. How many Services exist on the system?**
```sh
$ kubectl get services
$ kubectl get svc
```

**3. What is the type of the default kubernetes service?**
```sh
$ kubectl get services
```

**4. What is the targetPort configured on the kubernetes service?**
```
$ kubectl describe services
```

**5. How many labels are configured on the kubernetes service?**
```sh
$ kubectl describe service
```

**6. How many Endpoints are attached on the kubernetes service?**
```sh
$ kubectl describe service
```

**7. Create a new service to access the web application using the info below:**
```yaml
Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector: simple-webapp
```

```YAML
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 8080
      port: 8080
      nodePort: 30080
  selector:
    name: simple-webapp
```

```sh
$ vi service-definition-1.yaml
$ kubectl apply -f service-definition-1.yaml 
```

e) Deployments
----
**1. How many Deployments exist on the system now?**
```sh
$ kubectl get deployment
```

**2. What is the image used to create the pods in the new deployment?**
```sh
$ kubectl describe deployment frontend-deployment
```

**3. Create "Deployment" using YAML based configuration file (deployment-definition-1.yaml)**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: mywebsite
    tier: frontend
spec:
  replicas: 4
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
  selector:
    matchLabels:
      app: myapp
```

```sh
$ kubectl apply -f deployment-definition-1.yaml
```

**4. Create a new Deployment with the below attributes using your own deployment definition file:**
```yaml
Name: httpd-frontend;
Replicas: 3;
Image: httpd:2.4-alpine
```

```sh
$ touch deployment-definition-2.yaml 
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      name: httpd-frontend
  template:
    metadata:
      labels:
        name: httpd-frontend
    spec:
      containers:
      - name: httpd-frontend
        image: httpd:2.4-alpine
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
```

```sh
$ kubectl apply -f deployment-definition-2.yaml
```


e) Deployments - Update and Rollback
----

**1. create a deployment and tracks the cause of the change**
```sh
$ kubectl create -f deployment.yml --record
```

**2. Check the status of the deployment**
```sh
$ kubectl rollout status deployment/myapp-deployment
```

**3. Check the history of the deployment**
```sh
$ kubectl rollout history deployment/myapp-deployment
```

**4. Making a change to the deployment - change an image to a lower version**
```sh
$ kubectl describe deployment myapp-deployment
$ kubectl edit deployment myapp-deployment --record
$ kubectl set image deployment myapp-deployment nginx=nginx:1.18 --record
$ kubectl rollout history deployment/myapp-deployment.apps/myapp-deployment
```

**5. Revert back to the previous version of the nginx image**
```sh
$ kubectl rollout undo deployment/myapp-deployment
```
```sh

# This way of updating the image could result in the deployment-definition file having a different configuration
$ kubectl set image deployment/myapp-deployment \ nginx=nginx:1.9.1 
$ kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1

# Update the deployments after editing the yaml file
$ kubectl apply –f deployment-definition.yml

# view the rollout history of a deployment
$ kubectl rollout history deployment/myapp-deployment
```


f) Scheduling
----
**1. A pod definition file nginx.yaml is given. Create a pod using the file.**
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
```

```sh
$ kubectl apply -f nginx.yaml
```

**2. Why is the POD "nginx" is in a pending state?**
```sh
$ kubectl -n kube-system get pods # no Scheduler present
```

**3. Manually schedule the pod on node01. Delete and recreate the POD if necessary.**
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
```
```sh
$ kubectl delete pod nginx
$ kubectl apply -f nginx.yaml
```

**4. Now schedule the same pod on the master node. Delete and recreate the POD if necessary.**
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: master
  containers:
  -  image: nginx
     name: nginx
```
```sh
$ kubectl delete pod nginx
$ kubectl apply -f nginx.yaml
```

g) Namespaces
----
**1. How many Namespaces exist on the system?**
```sh
$ kubectl get namespaces
$ kubectl get ns --no-headers | wc -l
```

**2. How many pods exist in the research namespace?**
```sh
$ kubectl get pods --namespace=research
$ kubectl -n research get pods --no-headers
```

**3. Create a POD in the finance namespace - Pod name: redis; Image Name: redis.**
```sh
$ kubectl run redis --image=redis --dry-run=client -o yaml > pod.yaml
```

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
  namespace: finance #namespace added here#
spec:
  containers:
  - image: redis
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```sh
$ kubectl apply -f pod.yaml
$ kubectl -n finance get pod redis
```

OR

```sh
$ kubectl run redis --image=redis --namespace=finance
$ kubectl run redis --image=redis -n finance
$ kubectl -n finance get pod redis
```

**4. Check the pods in all namespaces**
```sh
$ kubectl get pods --all-namespaces
```

**5. Check the services in the "dev" namespace**
```sh
$ kubectl -n dev get services
```


h) Labels and Selectors
----
**1. We have deployed a number of PODs. They are labelled with tier, env and bu. How many PODs exist in the dev environment? Use selectors to filter the output**
```sh
$ kubectl get pods --selector env=de
```

**2. How many PODs are in the finance business unit (bu)?**
```sh
$ kubectl get pods --selector bu=finance
```

**3. How many objects are in the prod environment including PODs, ReplicaSets and any other objects?**
```
$ kubectl get all --selector env=prod
$ kubectl get all --selector env=prod --no-headers | wc -l
```

**4. Identify the POD which is part of the prod environment, the finance BU and of frontend tier?**
```sh
$ kubectl get all --selector env=prod,bu=finance,tier=frontend
```


i) Logging & Monitoring - viewing perfomance metrics on Kubernetes Cluster
----

Cluster Components

**1. Identify the node that consumes the most CPU / Memory.**
```sh
$ kubectl top node
```

**2. Identify the POD that consumes the most Memory.**
```sh
$ kubectl top pods
```

**3. Identify the POD that consumes the least CPU.**
```sh
$kubernetes-metrics-server# kubectl top pod --sort-by='cpu' --no-headers | tail -1 => tail -1 to list the last pod in the list 
```


Reference
----
[Kubernetes Official Documentation ](https://www.tutorialspoint.com/kubernetes/index.htm) </br>
[kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)</br>
[Kubernetes Tutorials Point ](https://www.kaggle.com/)
