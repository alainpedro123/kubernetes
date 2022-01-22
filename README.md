Kubernetes Fundamentals - Exercises 💻🚀
===============================

a) Getting started
----
1. What is the version of Kubernetes running on the nodes?

b) PODs with YAML
----
1. Create a new pod with the nginx image.
2. List the all PODs that have been created.
3. What is the image used to create the new pods?
4. Which nodes are these pods placed on?
5. How many containers are part of the pod webapp?
6. What images are used in the new webapp pod?
7. What is the state of the container agentx in the pod webapp?
8. Why do you think the container agentx in pod webapp is in error?
9. What does the READY column in the output of the "kubectl get pods" command indicate?
10. Delete the webapp Pod. Once deleted, wait for the pod to fully terminate.
11. Create a POD using YAML based configuration file below (pod.yml)
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

12. Create a new pod with the name redis and with the image redis123. Use a pod-definition YAML file below. (the image name is wrong)
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

c) Imperative commands
---
1. Deploy a pod named nginx-pod using the nginx:alpine image.
2. Deploy a redis pod using the redis:alpine image with the labels set to tier=db.
3. Create a service redis-service to expose the redis application within the cluster on port 6379.
* Service: redis-service
* Port: 6379
* Type: ClusterIP

4. Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas.
5. Create a new pod called custom-nginx using the nginx image and expose it on container port 8080.
6. Create a new namespace called dev-ns.
7. Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.
8. Create a pod called "httpd" using the image "httpd:alpine" in the default namespace. Next, create a service of type "ClusterIP" by the same name (httpd). The target port for the service should be 80.


c) Replication Controller / ReplicaSets
----
1. Create "Replication Controller" using YAML based configuration file (rc-definition.yml)
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

2. How many ReplicaSets exist on the system?
3. How about now? How many ReplicaSets do you see?
4. How many PODs are DESIRED in the new-replica-set?
5. Create "Replica Set" using YAML based configuration file (rc-definition.yml)
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

6. Update the replica to scale from 3 to 6
7. What is the image used to create the pods in the new-replica-set?
8. How many PODs are READY in the new-replica-set?
9. Why "new-replica-set" is not on READY status?
10. Delete any of the 4 existing PODs
11. How many PODS exist now?
12. Why are there still 4 PODs, even after you deleted one?
13. Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root/. There is an issue with the file, so try to fix it.
14. Create a ReplicaSet using the replicaset-definition-2.yaml file located at /root/. There is an issue with the file, so try to fix it.
15. Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2
16. Fix the original replica set new-replica-set to use the correct busybox image. Either delete and recreate the ReplicaSet or Update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.

17. Scale the ReplicaSet to 5 PODs. Use kubectl scale command or edit the replicaset using kubectl edit replicaset.
18. Now scale the ReplicaSet down to 2 PODs.


d) Services
----
1. How many Services exist on the system?
2. How many Services exist on the system?
3. What is the type of the default kubernetes service?
4. What is the targetPort configured on the kubernetes service?
5. How many labels are configured on the kubernetes service?
6. How many Endpoints are attached on the kubernetes service?
7. Create a new service to access the web application using the info below:
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

e) Deployments
----
1. How many Deployments exist on the system now?
2. What is the image used to create the pods in the new deployment?
3. Create "Deployment" using YAML based configuration file (deployment-definition-1.yaml)
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

4. Create a new Deployment with the below attributes using your own deployment definition file:
```yaml
Name: httpd-frontend;
Replicas: 3;
Image: httpd:2.4-alpine
```

f) Deployments - Update and Rollback
----
1. create a deployment and tracks the cause of the change
2. Check the status of the deployment
3. Check the history of the deployment
4. Making a change to the deployment - change an image to a lower version
5. Revert back to the previous version of the nginx image


g) Scheduling
----
1. A pod definition file nginx.yaml is given. Create a pod using the file.
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

2. Why is the POD "nginx" is in a pending state?
3. Manually schedule the pod on node01. Delete and recreate the POD if necessary.
4. Now schedule the same pod on the master node. Delete and recreate the POD if necessary.


h) Manual Scheduling
---
1. Manually schedule the pod on node01.
2. Manually schedule the pod on master.


i) Taints and Tolerations
---
1. Check if any taints exist on node node01?
2. Create a taint on node01 with:
```
Key = spray
Value = mortein
Effect = NoSchedule
```

3. Create another pod named bee with the nginx image, which has a toleration set to the taint mortein.
```
Image name: nginx
Key: spray
Value: mortein
Effect: NoSchedule
Status: Running
```

4. Check if any taints exist on master node?
5. Remove the taint on controlplane, which currently has the taint effect of NoSchedule.


j) Node Affinity
---
1. Create a deployment with the following: name -> blue; image -> nginx; replicas -> 3
2. Set Node Affinity to the deployment to place the pods on node01 only.
3.Create a new deployment named red with the nginx image and 2 replicas, and ensure it gets placed on the controlplane node only. Use the label - node-role.kubernetes.io/master - set on the controlplane node.

k) DaemonSets
---
1.Get all DaemonSets that are created in the cluster in all namespaces?
2. Check onhow many nodes are the pods scheduled by the DaemonSet kube-proxy
3.Deploy a DaemonSet for FluentD Logging. Use the given specifications:
```
Name: elasticsearch
Namespace: kube-system
Image: k8s.gcr.io/fluentd-elasticsearch:1.20
```

l) Static Pods
---

1. Check the number of static pods in this cluster in all namespaces?
2. Check on which nodes the static pods are created currently?
3. What is the path of the directory holding the static pod definition files?
4. How many pod definition files are present in the manifests folder?
5. What is the docker image used to deploy the kube-api server as a static pod?
6. Create a static pod named "static-busybox" that uses the "busybox" image and the command "sleep 1000"


m) Multiple Schedulers
---
1.Deploy an additional scheduler to the cluster following the given specification. Use the manifest file used by kubeadm tool. Use a different port than the one used by the current one.

```YAML
Namespace: kube-system
Name: my-scheduler
Status: Running
Custom Scheduler Name
```

```YAML
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: my-scheduler
    tier: control-plane
  name: my-scheduler
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=false
    - --port=10282
    - --scheduler-name=my-scheduler
    - --secure-port=0
    image: k8s.gcr.io/kube-scheduler:v1.19.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: 10282
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-scheduler
    resources:
      requests:
        cpu: 100m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: 10282
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/kubernetes/scheduler.conf
      name: kubeconfig
      readOnly: true
  hostNetwork: true
  priorityClassName: system-node-critical
  volumes:
  - hostPath:
      path: /etc/kubernetes/scheduler.conf
      type: FileOrCreate
    name: kubeconfig
status: {}
```

2. A POD definition file is given. Use it to create a POD with the new custom scheduler.

```
Name: nginx
Uses custom scheduler
Status: Running
```

```YAML
$ ---
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  schedulerName: my-scheduler
  containers:
  - image: nginx
    name: nginx
```


n) Namespaces
----
1. How many Namespaces exist on the system?
2. How many pods exist in the research namespace?
3. Create a POD in the finance namespace - Pod name: redis; Image Name: redis.
4. Check the pods in all namespaces
5. Check the services in the "dev" namespace


o) Labels and Selectors
---
1. How many PODs exist in the dev environment?
2. How many PODs are in the finance business unit (bu)?
3. How many objects are in the prod environment including PODs, ReplicaSets and any other objects?
4. Identify the POD which is part of the prod environment, the finance BU and of frontend tier?
5. Apply a label color=blue to node node01.



p) Logging & Monitoring - viewing perfomance metrics on Kubernetes Cluster
----

Cluster Components

1. Identify the node that consumes the most CPU / Memory.
2. Identify the POD that consumes the most Memory.
3. Identify the POD that consumes the least CPU.
