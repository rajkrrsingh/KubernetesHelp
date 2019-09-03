# Kubernetes Commands for day to day Work

***Kubernetes Components****
- Master
    - etcd distributed persistent storage
    - API Server
    [kubeapiserver](https://github.com/kubernetes/kubernetes/tree/master/pkg/kubeapiserver)

    - Scheduler
    [scheduler pkg](https://github.com/kubernetes/kubernetes/tree/master/pkg/scheduler)
    - Controller Manager
    [controller](https://github.com/kubernetes/kubernetes/tree/master/pkg/controller)

- Worker Nodes
    - Kubelet
    - Kube-proxy
    - Container Runtime
    
- Add ons
    - K8s DNS Server
    - Dashboard
    - Ingress Controller
    - Container Network Interface Plugin
    
***Controllers running in the Controller Manager***

    - Node controller
    - Replication Manager
    - Namespace controller
    - Service controller
    - Endpoints controller
    - ReplicaSet, DaemonSet and Job controllers
    - Deployment controller
    - StatefulSet controller
    - Namespace controller
    - PersistentVolume controller
    
***Get where the Master is running in the cluster***
```kubernetes helm
kubectl cluster-info 
Kubernetes master is running at https://172.26.88.185:6443
KubeDNS is running at https://172.26.88.185:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```

***Check Component status***
```kubernetes helm
kubectl get componentstatuses
NAME                 STATUS    MESSAGE             ERROR
controller-manager   Healthy   ok                  
scheduler            Healthy   ok                  
#etcd-0               Healthy   {"health":"true"}   
```

***Kubernetes components running as pods***
```kubernetes helm
kubectl get po   -n kube-system -o wide
NAME                                       READY   STATUS    RESTARTS   AGE    IP                NODE        NOMINATED NODE   READINESS GATES
calico-kube-controllers-65b8787765-dw55f   1/1     Running   0          2d5h   192.168.160.129   rksnode-1   <none>           <none>
calico-node-jp9pt                          1/1     Running   0          2d5h   172.26.88.186     rksnode-2   <none>           <none>
calico-node-lx7qj                          1/1     Running   0          2d5h   172.26.88.187     rksnode-3   <none>           <none>
calico-node-srr2f                          1/1     Running   0          2d5h   172.26.88.185     rksnode-1   <none>           <none>
coredns-5c98db65d4-2pwzl                   1/1     Running   0          2d5h   192.168.160.131   rksnode-1   <none>           <none>
coredns-5c98db65d4-9x2ld                   1/1     Running   0          2d5h   192.168.160.130   rksnode-1   <none>           <none>
etcd-rksnode-1                             1/1     Running   0          2d5h   172.26.88.185     rksnode-1   <none>           <none>
kube-apiserver-rksnode-1                   1/1     Running   0          2d5h   172.26.88.185     rksnode-1   <none>           <none>
kube-controller-manager-rksnode-1          1/1     Running   0          2d5h   172.26.88.185     rksnode-1   <none>           <none>
kube-proxy-5hr5x                           1/1     Running   0          2d5h   172.26.88.186     rksnode-2   <none>           <none>
kube-proxy-7rng8                           1/1     Running   0          2d5h   172.26.88.187     rksnode-3   <none>           <none>
kube-proxy-sqq4b                           1/1     Running   0          2d5h   172.26.88.185     rksnode-1   <none>           <none>
kube-scheduler-rksnode-1                   1/1     Running   0          2d5h   172.26.88.185     rksnode-1   <none>           <none>

### pods running on rksnode-1 belongs to master components
### No component directly talk to etcd, API server is to serve the request to modify cluster state and talk to etcd
```

***Look up etcld****
```kubernetes helm
curl -L https://github.com/coreos/etcd/releases/download/v3.2.10/etcd-v3.2.10-linux-amd64.tar.gz | gunzip -c | tar xO etcd-v3.2.10-linux-amd64/etcdctl > etcdctl && chmod +x etcdctl
export ETCDCTL_API=3
etcld get /registry
```

***Watching events emitted by controller***
```kubernetes helm
kubectl get events --watch
```
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

***Using Secrets***

command format : kubectl create secret $type $name $data

type:
```kubernetes helm
generic 
    - File
    - Directory
    - Literal values
docker registry
tls
``` 

data:
```kubernetes helm
--from-file or --from-literal
```

e.g.
```kubernetes helm
echo "myusername" > username.txt
echo "mypassword" > password.txt

kubectl create secret generic service-user-pass --from-file=username.txt --from-file=password.txt 
secret/service-user-pass created

kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-lrm66   kubernetes.io/service-account-token   3      2d
service-user-pass     Opaque                                2      73s

kubectl describe secret service-user-pass
Name:         service-user-pass
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password.txt:  11 bytes
username.txt:  11 bytes

```


- Creating Secret Manually
```kubernetes helm
echo -n "myusername" | base64
bXl1c2VybmFtZQ==

echo -n "mypassword" | base64
bXlwYXNzd29yZA==

kubectl create -f mysecret-def.yml 
secret/mysecret created

 kubectl get secret mysecret -o yaml
apiVersion: v1
data:
  password: bXlwYXNzd29yZA==
  username: bXl1c2VybmFtZQ==
kind: Secret
metadata:
  creationTimestamp: "2019-09-03T00:09:14Z"
  name: mysecret
  namespace: default
  resourceVersion: "259133"
  selfLink: /api/v1/namespaces/default/secrets/mysecret
  uid: 8938d31d-ec47-4ca9-8237-ed5362116c15
type: Opaque

```
[create secret manually](../master/src/resources/mysecret-def.yml)

- consuming secret in Pods
    - using volume
    - using env variables
    
[create pod which mount secret volume in pod](../master/src/resources/mysecret-pod-using-vol.yml)

```kubernetes helm

 kubectl create -f mysecret-pod-using-vol.yml 
pod/mypod created

 kubectl get po | grep mypod
mypod                               1/1     Running     0          68s

 kubectl exec mypod ls /etc/foo
password
username

kubectl exec mypod cat /etc/foo/username
myusername

kubectl exec mypod cat /etc/foo/password
mypassword
```

[create pod which uses secret in env](../master/src/resources/mysecret-pod-using-env.yml)
```kubernetes helm
kubectl create -f mysecret-pod-using-env.yml 

kubectl get po mypod-with-env
NAME             READY   STATUS    RESTARTS   AGE
mypod-with-env   1/1     Running   0          21s

kubectl exec mypod-with-env env | grep MY
MY_USER_NAME=myusername
MY_USER_PASSWORD=mypassword
```