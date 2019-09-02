# Kubernetes Commands for day to day Work

***List cluster nodes***
```kubernetes helm
kubectl get node
```

**List all namespaces**
```bash
kubectl get ns
```
**List pods in a namespace**
```kubernetes helm
kubectl get po --namespace default
```

**Creating a namespace from yaml file**
```kubernetes helm
kubectl create namespace dummyns
namespace/dummyns created

```
 [or using YAML](../master/src/resources/mynamespace-def.yml)
 
 **Creating pod in namespace***
 ```kubernetes helm
 kubectl create -f pod-definition.yml -n dummyns
pod/myapp-pod created
```
 
**Creating a pod**
[create pod using yml](../master/src/resources/pod-definition.yml)

 
**Getting Full YAML of deployed POD**
```aidl
kubectl get po myapp-pod -o yaml
kubectl get po myapp-pod -o json
```

**Get Pod's Logs**

`
kubectl logs myapp-pod ( -c container name if want to fetch the specific container logs)
`

**List pod with their labels**

```
kubectl get pod -show-lablels
myapp-pod                           1/1     Running   0          25h     app=myapp,type=front-end

kubectl label po myapp-pod type=backend --overwrite
pod/myapp-pod labeled

kubectl get pod --show-labels
myapp-pod                           1/1     Running   0          25h     app=myapp,type=backend

 kubectl label po myapp-pod standalone=true
pod/myapp-pod labeled
kubectl get pod --show-labels
myapp-pod                           1/1     Running   0          25h     app=myapp,standalone=true,type=backend

```

**Listing Pod using label selector**
```kubernetes helm
kubectl get po -l standalone=true 
NAME        READY   STATUS    RESTARTS   AGE
myapp-pod   1/1     Running   0          25h

those who dont have standalone labels can be queried using
kubectl get po -l '!standalone' 

```

***Deleting Pod***
```kubernetes helm
kubectl delete po mypod
```

***Deleting pods using label selector***
```kubernetes helm
kubectl delete po -l type=backend
pod "myapp-pod" deleted
```

***ReplicationController***

[Create ReplicationController using YAML](../master/src/resources/rc-def.yml)

***Edit ReplicationController***
```kubernetes helm
kubectl edit rc myapp-rc
```

***Scaling ReplicationController***
```kubernetes helm
kubectl scale rc myapp-rc --replicas=5
 kubectl get rc 
NAME       DESIRED   CURRENT   READY   AGE
myapp-rc   5         5         5       28h
```

***Replica Set***

[create replicaset using yaml](../master/src/resources/replicaset-def.yml)

```kubernetes helm
# kubectl create -f replicaset-def.yml
# kubectl get pods
# scale using kubectl scale --replicas=6 -f replicaset-def.yml
# kubectl get replicaset /kubectl get rs
# kubectl delete replicaset myapp-replicaset
```


***DAEMONSET: To Deploy ONLY ONE NODE on every node or subset of nodes inside cluster***

[create daemonset using yaml definition](../master/src/resources/daemonset-def.yaml)

```kubernetes helm
kubectl create -f daemonset-def.yaml
kubectl get ds
kubectl get pod -o wide 
kubectl describe ds fluentd-ds
```

***JOB: Run Job until completion(e.g. Batch Job)***

- each job creates one or more Pods, 
- Ensure they are successfully terminated, 
- can run multiple pods in parallel, 
- can scale up/down pods 

[Create Job using yaml definition](../master/src/resources/job-def.yml)

```kubernetes helm
kubectl create -f job-def.yml 
kubectl get job
kubectl describe jobs printenv
kubectl get pods
printenv-crd9l                      0/1     Completed   0          4m42s <- completed

check the output of completed job
kubectl logs printenv-crd9l
```

***Scaling a Job***

```kubernetes helm
kubectl scale job printenv --replicas 3
```

***CRON Job***

[create cron job using yaml definition](../master/src/resources/cronjob-def.yml)

```kubernetes helm
kubectl create -f cron-job-def.yml 
cronjob.batch/printimenow created

kubectl get cronjob
NAME          SCHEDULE             SUSPEND   ACTIVE   LAST SCHEDULE   AGE
printimenow   0,15,30,45 * * * *   False     0        <none>          17s

kubectl describe cronjob printimenow

### Create watch on Job
kubectl get jobs --watch


kubectl delete cronjob printimenow

```