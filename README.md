# KubernetesHelp

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
 [or using YAML]( ../src/resources/mynamespace-def.yml )
 
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

