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

kubectl logs printimenow-1567405800-p86vg    
Mon Sep  2 06:30:41 UTC 2019


### Create watch on Job
kubectl get jobs --watch


kubectl delete cronjob printimenow

```

***Getting pod enviornment variables***
```kubernetes helm
kubectl exec myapp-pod env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=myapp-rc-nhcgr
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
NGINX_VERSION=1.17.3
NJS_VERSION=0.3.5
PKG_RELEASE=1~buster
HOME=/root

-- Inside the container
kubectl exec -it myapp-rc-nhcgr bash
root@myapp-rc-nhcgr:/# 
```

***Get the details of service deployed***
```kubernetes helm
kubectl get svc
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        43h
myapp-service   NodePort    10.104.223.51   <none>        80:30008/TCP   19h

 kubectl describe svc myapp-service
Name:                     myapp-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=myapp,type=front-end
Type:                     NodePort
IP:                       10.104.223.51
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30008/TCP
Endpoints:                192.168.0.66:80,192.168.0.68:80,192.168.0.69:80 + 5 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

```

***Display the service endpoint***
```kubernetes helm
kubectl get endpoints myapp-service
```

***LoadBalancer Service****

[Create LoadBalancer Service using yaml definition](../master/src/resources/loadbalance-def.yml)

```kubernetes helm
 kubectl create -f loadbalancer-def.yml 
get svc myapp-loadbalance-service
NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
myapp-loadbalance-service   LoadBalancer   10.103.29.113   <pending>     80:30044/TCP   2m34s

 kubectl describe service myapp-loadbalance-service
Name:                     myapp-loadbalance-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=myapp
Type:                     LoadBalancer
IP:                       10.103.29.113
Port:                     <unset>  80/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30044/TCP
Endpoints:                192.168.0.66:8080,192.168.0.68:8080,192.168.0.69:8080 + 5 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

```

***Discovering Pod through DNS***

```kubernetes helm
kubectl run dnsutils --image=tutum/dnsutils --generator=run-pod/v1 --command -- sleep infinity
pod/dnsutils created

kubectl get po dnsutils
NAME       READY   STATUS    RESTARTS   AGE
dnsutils   1/1     Running   0          39s

kubectl exec dnsutils nslookup nginx-7bb7cd8db5-wnpwd
Server:		10.96.0.10
Address:	10.96.0.10#53

** server can't find nginx-7bb7cd8db5-wnpwd: NXDOMAIN

command terminated with exit code 1
```

***Volumes****
- emptyDir :- simple empty directory to store transient data, data deleted when pod die.
- hostPath :- mounting host node directories directly to pod.
- nfs :- NFS mount to the pod.
- gitRep :- volume to initialized git repo.
- Cloud provider specific storage:-
    - gcePersistentDisk :- google compute engine persistent disk
    - awkElasticBlockStore :- AWS elastic blockstore volume.
    - azureDisk :- MS azure disk volume 
- Network storage :-
    - [cinder](https://docs.okd.io/latest/install_config/persistent_storage/persistent_storage_cinder.html)
    - [cephFS](https://docs.ceph.com/docs/master/cephfs/)
    - [glusterfs](https://github.com/gluster/gluster-kubernetes)
    - [Flexvolume](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md) 
- ConfigMap,secret,downwardAPI :- kubernetes provided special volume to expose resources to pod/containers
- PersistentVolumeClaim :- dynamically provisioned persistent storage.

A Pod can use multiple volumes of different types at same time, each of the pod's can have volume mounted or not.

***emptyDir***

[create emptyDir using yaml definition](../master/src/resources/pod-with-emptydir-def.yml)

```kubernetes helm
kubectl create -f pod-with-empty-dir.yml 
pod/myapp-with-emptydir created

kubectl get po | grep empty
myapp-with-emptydir                 1/1     Running     0          2m27s

kubectl exec myapp-with-emptydir df /tmp/
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/vda1       83872132 3336752  80535380   4% /tmp

kubectl exec myapp-with-emptydir df /tmp/emptydir
#Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/vda1       83872132 3336460  80535672   4% /tmp/emptydir

kubectl describe pod myapp-with-emptydir 

-- check for
Mounts:
      /tmp/emptydir from tmp-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-lrm66 (ro)
```

***HostPath***

[Create HostPath using yaml definition](../master/src/resources/hostPath-def.yml)

```kubernetes helm
kubectl create -f hostpath-def.yml 
kubectl get po myapp-with-hostpath -o wide
NAME                  READY   STATUS    RESTARTS   AGE   IP                NODE        NOMINATED NODE   READINESS GATES
myapp-with-hostpath   1/1     Running   0          18s   192.168.121.140   rksnode-2   <none>           <none>

now on rksnode-2
ll /tmp/test-vol/
total 0

echo "hello hostpath" > /tmp/test-vol/file.txt

kubectl exec -it myapp-with-hostpath bash
root@myapp-with-hostpath:/# ls /my-mnt/
file.txt
```

***Persistent Volume***
Abstract details of how the storage is provided from how it is consumed, consist mainly 2 parts Persistent volume (PV) and Persistent volume claim (PVC)



provisioning (Admin provision it)  -> binding (dev binds and use it) -> using  -> reclaiming

provisioning: static vs dynamic

more to be added.....TODO


***ConfigMap***

Decouple configuration from containers and Pods, store configs in key/value pair

```kubernetes helm
Creating Config Map
kubectl create configmap <map-name> <data-source>
data-source 
- path to dir/file: -from-file
- key value pair: -from-literal


kubectl create configmap emp-config --from-file=/tmp/myconfdir/
configmap/emp-config created

kubectl get configmaps -o wide
NAME         DATA   AGE
emp-config   1      35s

kubectl get configmaps -o yaml
apiVersion: v1
items:
- apiVersion: v1
  data:
    myconf.properties: "name: Alex \nage: 35\n"
  kind: ConfigMap
  metadata:
    creationTimestamp: "2019-09-02T23:27:08Z"
    name: emp-config
    namespace: default
    resourceVersion: "255271"
    selfLink: /api/v1/namespaces/default/configmaps/emp-config
    uid: 881afe6b-fb98-4be4-ad53-830e3f66ecd5
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""


- Creating config map using literal 
kubectl create configmap myliteralconfig --from-literal=myname=raj
configmap/myliteralconfig created
```

refrencing the configmap in pod[ create pod which refer our config map](../master/src/resources/configmaptest-pod-def.yml)

```kubernetes helm
 kubectl create -f configmap-test.yml
kubectl logs printconfigmap
MY_NAME_KEY=raj
. 
...
```