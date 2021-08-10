# Kubernetes

Deployment, automate and scale containers, 
Foster off an ecosystem of comonents and tools that makes running containers eaasy. 
Started at Google. 
Borg was predecessor to K8s. 
Helps you scheldule and run containers on machines.
Can use any container platform, but docker is most common. 
Pokemon Go is the largest deployment off k8s.

Features:
- Multihost container scheduling
-- Done by thhe kube-scheduler. 
--Assigns pods to notes at runtime.
--Checks resources, quality, policies and specifications before scheduling. 

- Scalability and deployability
 -- Up to 5000 node clusters
 -- 150,000 total pods. 
 -- plug and play architecture - add ons whiich can be added when needed. 
 -- registration and discovery
 - Automatic detection and registry
 - persistent storage - allows that 
 
 
 API server - allows interaction of the API, helps you control K8s. 
 Scheduler - watches created pods and assigns pods to run on nodes. 
 Controllerr manager - runs controllers- background threads. 
 etcd - stores all data here, in a simple KV store. 
 kubectl - comand line interface here. 
 Worker nodes - nodes where applications opperate.
 kubelet - components off worker nodess in combination with the API server, Mounts volumes using the container engine
 Docker runs containers on the nodes.
 kube-proxy - nettwork proxy and load balancy on a single worker node. handles network routing.
 Containers are coupled in pods. 
 Pods are the smallest unit of deployment. 
 Share storage, ip addresses and linux namespace. 
 kkubelet tracks pod state. 
 Node is the owrker machine in K8s cluster, 
 Each node needs a kubelet, containerr tooling (docker) a proxy proces running, Superrvisord
 It's reccomended to have a 3 node cluter.
 Use Minikube....
 Pod - represents one running process on  cluster.
 Contains docker application container, storage resources, unique network IP, 
 Never create pods by themselves. 
 Use higher level constructs - don't use pods directly ,use a controller instead like a deployment. 
 Pod states:
- Pending (accepted by kubernetes system)
- Running 
- Succeeded (sucessful execution)
- failed (all exited with one with a non0 state. 
- Crashloop backoff - fais to start but can't restart it. 
Controllers
- Reliability, scalability, load balancing
- Types:
--replica sets - ensures the speified number of replicas for a pod are running at all times. 
--Deployment - deployment controller provides declaraive updates for ppods and replicasets. 
--Pod Management: running a replicaset allows us to deploy a number of pods, check their status as a single unit. 
--Scaling a replicaset scales out the pods and allows for rollback.
--Pause and resune - pause, make updates, resume deployment. 
--status easy to check the health of pods.
Replication controller - ignore as replaced with deployments and replicasets instead.
DaemonSets
- ensures all nodes run  copy of a specific pod. 
Jobs
Supervisor process for pods carrying out batch jjobs.
Services - creates connectivity between replica sets. 
kinds of services - internal IP: only reachable within the cluster, external: endpoint availablethrough node ip. 


Labels, selectors and namespaces
- allow us to annotate and organise the application. 
- key-value pairs that are attached to objcts like pods. 
- used to organise clusters. 
- Unique per object. 
- labels are useful with selectors - you can identify a specific set of objects.
- Equalitty selectors - uses equality and inequality (tested on label value).
- Set baased: In and not in. Value is in a set or not in a set of values .

Namespaces
-Allows people to access resources with accountability.
- good way to divide cluster resources between users
- provide scope for names- must be unique withiin the namespace. 
-- new applications install their resources in a different namespace. 

Kubelet and kube-proxy:
- the kubernetes node agent. 
- kubelet executes pods, health checks, mounts pods etc.
- podspec - yaml fine that describes a pod.
- only manages containers created by the API server. 
- Network proxy - kubeproxy.
- Modes:
--user space mode
--iptables mode
--Ipvs mode.
Modes are important...


## Some basic commands

### Pods
Get pods and delete things:
```bash
kubectl get pods
     --show-labels
     --selector <dev-lead>=<name>,<env>=<label2> #Selects pods 
     -l 'release-version in (1.0, 2,0)' #label with release version between 1 and 2, can use notin for negative. 

kubectl delete pods
     -l <dev-lead>=<name>  #-l flag for label. 

kubectl get deployments

kubectl create -f filename.yaml #initialise deployment in .yaml file. 
     --record - records the rollout history,.

kubectl get replicaset

kubectl describe po/<pod name> # describe current status of the pod. 

#Update deployment
kubectl set image deployment/navbar-deployment helloworld=karthequian/helloworld:blue
#Undo update:
kubectl rollout undo deployment/navbar-deployment

kubectl rollout history deployment/navbar-deployment

kubectl logs <pod name>

kubectl exec -it <pod name> /bin/bash #Execs into pod and runs a bash terminal. 

kubectl create configmap logger --from-literal=log_level=debug

kubectl create secret generic <secret name> --from-literal=<key>=<value> #creates a secret key
kubectl get secrets #shows secrets.
    - <secret name> #lists secret properties
    - <secret name> -o yaml #view secret yaml file. 
    
kubectl get gronjob
kubectl get jobs

kubectl create daemonset. 



```
### Cronjobs
For cronjobs - you canstart and stop by adjusting the 'suspend' status. True = stop, False = running.

### DaemonSet
ensures all nodes run a copy of specific pods. 

### StatefulSet
Stateful sets manage the deployment and scaling for a set of pods, and provide guarantees about the ordering and the uniqueness of these pods. But unlike a deployment, a stateful set manages the sticky identity for each of these pods.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secretreader
  labels:
    name: secretreader
spec:
  replicas: 1
  selector:
    matchLabels:
      name: secretreader
  template:
    metadata:
      labels:
        name: secretreader
    spec:
      containers:
      - name: secretreader
        image: karthequian/secretreader:latest
        env:
        - name: api_key
          valueFrom:
            secretKeyRef:
              name: apikey
              key: api_key
```

References:
https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

### Application health checks

readiness probe - used to know when the pod is ready to accept traffic. 

timeout seconds = amount of time to wait before timeing out.

livenessprobe - length of time to wait for pod to initialise.
The readinessProbe detects when a container can accept traffic, and the livenessProbe checks whether the container is alive and running.

```bash
minikube addons list #list addons

minikube addons enable <addon name> #Enables a particular addon. 

minikube <add-on> #runs the addon. 

```







 
