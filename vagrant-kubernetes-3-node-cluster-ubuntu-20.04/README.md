
#install step
##step1 
add /etc/vbox/networks.conf allow * 0.0.0.0/0 ::/0

```
root@vpp2:/home/cbcadmin/vagrant-kubernetes-3-node-cluster-ubuntu-20.04# __vi /etc/vbox/networks.conf__
* 0.0.0.0/0 ::/0
```
                                                                                                                                 
##step2
root@vpp2:/home/cbcadmin/vagrant-kubernetes-3-node-cluster-ubuntu-20.04# __vagrant up__<br>
或用vagrant up --provider libvirt 可以起的快一点

##step3
copy kubernetes config to local for kubecli
```
    scp root@172.16.16.100:/etc/kubernetes/admin.conf ~/.kube/config
    
    otherwise
    root@vpp2:/home/cbcadmin/kubernetes/vagrant-provisioning# kubectl get all
    The connection to the server localhost:8080 was refused - did you specify the right host or port?
    root@vpp2:/home/cbcadmin/kubernetes/vagrant-provisioning# kubectl cluster-info
    To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
    The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

##step4
kubectl get nodes
```
root@vpp2:/home/cbcadmin/kubernetes/vagrant-provisioning# kubectl get nodes
NAME       STATUS   ROLES           AGE    VERSION
kmaster    Ready    control-plane   147m   v1.24.0
kworker1   Ready    <none>          137m   v1.24.0
kworker2   Ready    <none>          127m   v1.24.0
```
    
##step5 
kubectl cluster-info
```
root@vpp2:/home/cbcadmin/kubernetes/vagrant-provisioning# kubectl cluster-info
Kubernetes control plane is running at https://172.16.16.100:6443
CoreDNS is running at https://172.16.16.100:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'
```


##step6
kubectl -n kube-system get all

```
root@vpp2:/home/cbcadmin/kubernetes/vagrant-provisioning# kubectl -n kube-system get all
NAME                                           READY   STATUS    RESTARTS       AGE
pod/calico-kube-controllers-5c49f86c48-wskdv   1/1     Running   0              149m
pod/calico-node-2bbck                          1/1     Running   0              139m
pod/calico-node-8q9f8                          1/1     Running   0              149m
pod/calico-node-jtn7w                          1/1     Running   0              129m
pod/coredns-6d4b75cb6d-c2cmq                   1/1     Running   0              149m
pod/coredns-6d4b75cb6d-ppnwp                   1/1     Running   0              149m
pod/etcd-kmaster                               1/1     Running   0              149m
pod/kube-apiserver-kmaster                     1/1     Running   0              149m
pod/kube-controller-manager-kmaster            1/1     Running   1 (147m ago)   149m
pod/kube-proxy-28q7z                           1/1     Running   0              139m
pod/kube-proxy-r87fh                           1/1     Running   0              149m
pod/kube-proxy-x8c7l                           1/1     Running   0              129m
pod/kube-scheduler-kmaster                     1/1     Running   1 (147m ago)   149m

NAME               TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   149m

NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
daemonset.apps/calico-node   3         3         3       3            3           kubernetes.io/os=linux   149m
daemonset.apps/kube-proxy    3         3         3       3            3           kubernetes.io/os=linux   149m

NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/calico-kube-controllers   1/1     1            1           149m
deployment.apps/coredns                   2/2     2            2           149m

NAME                                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/calico-kube-controllers-5c49f86c48   1         1         1       149m
replicaset.apps/coredns-6d4b75cb6d                   2         2         2       149m
```
    
    
##create nginx
kubectl create deploy nginx --image nginx
```
root@vpp2:/home/cbcadmin# kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-8f458dc5b-qd824   1/1     Running   0          2m38s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   156m

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           2m38s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-8f458dc5b   1         1         1       2m38s
```
kubectl expose deploy nginx --type NodePort --port 80
```
root@vpp2:/home/cbcadmin# kubectl expose deploy nginx --type NodePort --port 80
service/nginx exposed
```
kubectl get svc

```
root@vpp2:/home/cbcadmin# kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        171m
nginx        NodePort    10.106.2.168   <none>        80:30981/TCP   12s
```
kubectl get pod -o wide
```
root@vpp2:/home/cbcadmin# kubectl get pod -o wide
NAME                    READY   STATUS    RESTARTS   AGE   IP               NODE       NOMINATED NODE   READINESS GATES
nginx-8f458dc5b-qd824   1/1     Running   0          28m   192.168.77.129   kworker2   <none>           <none>
```

##REMOVE ALL
* vagrant destroy -f
* rm -rf ~./.kube
* mkdir ./.kube
* rm .ssh/known_hosts

* vbox这边会残留一些接口
```
vboxmanage list hostonlyifs
vboxmanage hostonlyif remove vboxnet0
```




