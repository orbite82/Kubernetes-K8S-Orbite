# Kubernetes-K8S-Orbite

* Instalação Kubernetes:

```

```
---

* Verifica nodes e informações

```

vagrant@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES    AGE    VERSION
k8s-master   Ready    master   6d2h   v1.17.2
node-1       Ready    <none>   6d2h   v1.17.2
node-2       Ready    <none>   6d2h   v1.17.2
vagrant@k8s-master:~$ kubectl describe node k8s-master
Name:               k8s-master
Roles:              master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=k8s-master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/master=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.16.1.10/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.235.192
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 05 Feb 2020 17:23:12 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  k8s-master
  AcquireTime:     <unset>
  RenewTime:       Tue, 11 Feb 2020 20:12:49 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Tue, 11 Feb 2020 18:33:22 +0000   Tue, 11 Feb 2020 18:33:22 +0000   CalicoIsUp                   Calico is running on this node
  MemoryPressure       False   Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:23:09 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:23:09 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:23:09 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:24:09 +0000   KubeletReady                 kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  172.16.1.10
  Hostname:    k8s-master
Capacity:
  cpu:                4
  ephemeral-storage:  64194404Ki
  hugepages-2Mi:      0
  memory:             2047672Ki
  pods:               110
Allocatable:
  cpu:                4
  ephemeral-storage:  59161562629
  hugepages-2Mi:      0
  memory:             1945272Ki
  pods:               110
System Info:
  Machine ID:                 5ebf5e5e0b5564f403ddc2e15e39d743
  System UUID:                C7FDDF66-0DD0-4EAA-8382-324569A29918
  Boot ID:                    334bf2e3-e237-49fe-8dd3-19237599b2f5
  Kernel Version:             4.4.0-173-generic
  OS Image:                   Ubuntu 16.04.6 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://19.3.5
  Kubelet Version:            v1.17.2
  Kube-Proxy Version:         v1.17.2
PodCIDR:                      192.168.0.0/24
PodCIDRs:                     192.168.0.0/24
Non-terminated Pods:          (9 in total)
  Namespace                   Name                                        CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                   ----                                        ------------  ----------  ---------------  -------------  ---
  kube-system                 calico-kube-controllers-5c45f5bd9f-69qmd    0 (0%)        0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 calico-node-f4stf                           250m (6%)     0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 coredns-6955765f44-22qr6                    100m (2%)     0 (0%)      70Mi (3%)        170Mi (8%)     6d2h
  kube-system                 coredns-6955765f44-5lqwv                    100m (2%)     0 (0%)      70Mi (3%)        170Mi (8%)     6d2h
  kube-system                 etcd-k8s-master                             0 (0%)        0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-apiserver-k8s-master                   250m (6%)     0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-controller-manager-k8s-master          200m (5%)     0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-proxy-68fnz                            0 (0%)        0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-scheduler-k8s-master                   100m (2%)     0 (0%)      0 (0%)           0 (0%)         6d2h
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                1 (25%)     0 (0%)
  memory             140Mi (7%)  340Mi (17%)
  ephemeral-storage  0 (0%)      0 (0%)
Events:              <none>

```
* Ativando o auto complete

```
root@k8s-master:~# source <(kubectl completion bash)
root@k8s-master:~# echo "source <(kubectl completion bash)" >> /root/.bashrc

```

* Procurar um namespace especifico

```
vagrant@k8s-master:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   6d3h
kube-node-lease   Active   6d3h
kube-public       Active   6d3h
kube-system       Active   6d3h
vagrant@k8s-master:~$ kubectl get pods -n kube-system
NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-5c45f5bd9f-69qmd   1/1     Running   0          6d3h
calico-node-f4stf                          1/1     Running   0          6d3h
calico-node-wrxb9                          1/1     Running   1          6d2h
calico-node-z4dsg                          1/1     Running   1          6d3h
coredns-6955765f44-22qr6                   1/1     Running   0          6d3h
coredns-6955765f44-5lqwv                   1/1     Running   0          6d3h
etcd-k8s-master                            1/1     Running   1          6d3h
kube-apiserver-k8s-master                  1/1     Running   1          6d3h
kube-controller-manager-k8s-master         1/1     Running   1          6d3h
kube-proxy-68fnz                           1/1     Running   1          6d3h
kube-proxy-h664c                           1/1     Running   1          6d2h
kube-proxy-l596h                           1/1     Running   1          6d3h
kube-scheduler-k8s-master                  1/1     Running   1          6d3h

```

* Usando o parametro -o wide, ele trás mais detalhes

```
vagrant@k8s-master:~$ kubectl get pods -n kube-system -o wide
NAME                                       READY   STATUS    RESTARTS   AGE    IP                NODE         NOMINATED NODE   READINESS GATES
calico-kube-controllers-5c45f5bd9f-69qmd   1/1     Running   0          6d3h   192.168.235.194   k8s-master   <none>           <none>
calico-node-f4stf                          1/1     Running   0          6d3h   172.16.1.10       k8s-master   <none>           <none>
calico-node-wrxb9                          1/1     Running   1          6d2h   172.16.1.12       node-2       <none>           <none>
calico-node-z4dsg                          1/1     Running   1          6d3h   172.16.1.11       node-1       <none>           <none>
coredns-6955765f44-22qr6                   1/1     Running   0          6d3h   192.168.235.193   k8s-master   <none>           <none>
coredns-6955765f44-5lqwv                   1/1     Running   0          6d3h   192.168.235.195   k8s-master   <none>           <none>
etcd-k8s-master                            1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-apiserver-k8s-master                  1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-controller-manager-k8s-master         1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-proxy-68fnz                           1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-proxy-h664c                           1/1     Running   1          6d2h   172.16.1.12       node-2       <none>           <none>
kube-proxy-l596h                           1/1     Running   1          6d3h   172.16.1.11       node-1       <none>           <none>
kube-scheduler-k8s-master                  1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>

```
---
---

# Como Executar um pod e verificar sua estrutura

```
vagrant@k8s-master:~$ kubectl run nginx --image nginx
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           59s

vagrant@k8s-master:~$ kubectl get replicasets.
NAME               DESIRED   CURRENT   READY   AGE
nginx-6db489d4b7   1         1         1       103s

vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6db489d4b7-wkpqj   1/1     Running   0          2m7s

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6d3h

vagrant@k8s-master:~$ kubectl describe replicasets. nginx-6db489d4b7
Name:           nginx-6db489d4b7
Namespace:      default
Selector:       pod-template-hash=6db489d4b7,run=nginx
Labels:         pod-template-hash=6db489d4b7
                run=nginx
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/nginx
Replicas:       1 current / 1 desired
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  pod-template-hash=6db489d4b7
           run=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  4m52s  replicaset-controller  Created pod: nginx-6db489d4b7-wkpqj

```

```
vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6db489d4b7-wkpqj   1/1     Running   0          8m15s
vagrant@k8s-master:~$ kubectl describe pods nginx-6db489d4b7-wkpqj
Name:         nginx-6db489d4b7-wkpqj
Namespace:    default
Priority:     0
Node:         node-2/172.16.1.12
Start Time:   Tue, 11 Feb 2020 20:32:31 +0000
Labels:       pod-template-hash=6db489d4b7
              run=nginx
Annotations:  cni.projectcalico.org/podIP: 192.168.247.1/32
Status:       Running
IP:           192.168.247.1
IPs:
  IP:           192.168.247.1
Controlled By:  ReplicaSet/nginx-6db489d4b7
Containers:
  nginx:
    Container ID:   docker://09c060755f6b21f60084e646279813a2ccd61b1991bfdb7d108d4f5f3eb17a08
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:ad5552c786f128e389a0263104ae39f3d3c7895579d45ae716f528185b36bc6f
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 11 Feb 2020 20:32:54 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned default/nginx-6db489d4b7-wkpqj to node-2
  Normal  Pulling    8m49s      kubelet, node-2    Pulling image "nginx"
  Normal  Pulled     8m30s      kubelet, node-2    Successfully pulled image "nginx"
  Normal  Created    8m30s      kubelet, node-2    Created container nginx
  Normal  Started    8m29s      kubelet, node-2    Started container nginx

```

```
vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           12m
vagrant@k8s-master:~$ kubectl describe deployments. nginx
Name:                   nginx
Namespace:              default
CreationTimestamp:      Tue, 11 Feb 2020 20:32:31 +0000
Labels:                 run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-6db489d4b7 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  13m   deployment-controller  Scaled up replica set nginx-6db489d4b7 to 1

```

* Executanto scale no nginx

```
vagrant@k8s-master:~$ kubectl scale --replicas=10 deployment nginx
deployment.apps/nginx scaled

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   2/10    10           2           16m

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   7/10    10           7           16m

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   10/10   10           10          16m

```

```
vagrant@k8s-master:~$ kubectl run nginx3 --image nginx
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx3 created
vagrant@k8s-master:~$ kubectl get deployments.
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
nginx    10/10   10           10          18m
nginx3   1/1     1            1           6s

```
---
---
# Como remover um deployment

```
vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6db489d4b7-49tzb   1/1     Running   0          4m47s
nginx-6db489d4b7-9rfhk   1/1     Running   0          4m47s
nginx-6db489d4b7-cttdx   1/1     Running   0          4m47s
nginx-6db489d4b7-gfxwn   1/1     Running   0          4m47s
nginx-6db489d4b7-m8xgf   1/1     Running   0          4m47s
nginx-6db489d4b7-nwn7c   1/1     Running   0          4m47s
nginx-6db489d4b7-pf9zc   1/1     Running   0          4m47s
nginx-6db489d4b7-rjzwx   1/1     Running   0          4m47s
nginx-6db489d4b7-wkpqj   1/1     Running   0          20m
nginx-6db489d4b7-x9bw9   1/1     Running   0          4m47s
nginx3-8f65b44f9-s6tlb   1/1     Running   0          2m19s

vagrant@k8s-master:~$ kubectl get deployments.
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
nginx    10/10   10           10          20m
nginx3   1/1     1            1           2m31s
vagrant@k8s-master:~$ kubectl delete deployments. nginx
deployment.apps "nginx" deleted
vagrant@k8s-master:~$ kubectl delete deployments. nginx3
deployment.apps "nginx3" deleted
vagrant@k8s-master:~$ kubectl get deployments.
No resources found in default namespace.
vagrant@k8s-master:~$ 

```
---
---

# Direcionando uma porta

```
vagrant@k8s-master:~$ kubectl run nginx --image nginx --port=80
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/1     1            0           4s

```
* Validando porta atrelada

```
vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-5578584966-6bh78   1/1     Running   0          3m25s

vagrant@k8s-master:~$ kubectl describe pods nginx-5578584966-6bh78
Name:         nginx-5578584966-6bh78
Namespace:    default
Priority:     0
Node:         node-1/172.16.1.11
Start Time:   Tue, 11 Feb 2020 20:56:51 +0000
Labels:       pod-template-hash=5578584966
              run=nginx
Annotations:  cni.projectcalico.org/podIP: 192.168.84.135/32
Status:       Running
IP:           192.168.84.135
IPs:
  IP:           192.168.84.135
Controlled By:  ReplicaSet/nginx-5578584966
Containers:
  nginx:
    Container ID:   docker://e465a521e4879b442a507ecb57894d3030345fa60d7cf20ceee524224e334a35
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:ad5552c786f128e389a0263104ae39f3d3c7895579d45ae716f528185b36bc6f
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 11 Feb 2020 20:56:57 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned default/nginx-5578584966-6bh78 to node-1
  Normal  Pulling    3m42s      kubelet, node-1    Pulling image "nginx"
  Normal  Pulled     3m39s      kubelet, node-1    Successfully pulled image "nginx"
  Normal  Created    3m39s      kubelet, node-1    Created container nginx
  Normal  Started    3m38s      kubelet, node-1    Started container nginx

```

```
vagrant@k8s-master:~$ kubectl get deployments. nginx -o yaml

```

`Padrão yaml`
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2020-02-11T20:56:51Z"
  generation: 1
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "187243"
  selfLink: /apis/apps/v1/namespaces/default/deployments/nginx
  uid: 3a6ba262-a84d-4206-9fbe-c1dad8774fcb
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: "2020-02-11T20:56:57Z"
    lastUpdateTime: "2020-02-11T20:56:57Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2020-02-11T20:56:51Z"
    lastUpdateTime: "2020-02-11T20:56:57Z"
    message: ReplicaSet "nginx-5578584966" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1

  ```
  vagrant@k8s-master:~$ kubectl get deployments. nginx -o yaml > primeiro_deployment.yaml

  ```
* yaml alterado:

```
vagrant@k8s-master:~$ cat primeiro_deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
  generation: 1
  labels:
    run: nginx
  name: my-nginx
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30

```
---
---
#  Criando o deployments

```
vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           19m

vagrant@k8s-master:~$ kubectl create -f primeiro_deployment.yaml 
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   1/1     1            1           26s
nginx      1/1     1            1           20m

```

# Removendo o deployments

```
vagrant@k8s-master:~$ kubectl delete -f primeiro_deployment.yaml 
deployment.apps "my-nginx" deleted
vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           22m

```

# Subindo com 10 replicas o deployments

```
vagrant@k8s-master:~$ vim primeiro_deployment.yaml

```
`Alterar apenas`
spec:
  progressDeadlineSeconds: 600
  replicas: 1

  --->

  spec:
  progressDeadlineSeconds: 600
  replicas: 10

```
vagrant@k8s-master:~$ kubectl create -f primeiro_deployment.yaml 
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   2/10    10           2           10s
nginx      1/1     1            1           25m

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   6/10    10           6           30s
nginx      1/1     1            1           25m

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   10/10   10           10          49s
nginx      1/1     1            1           26m

```
---
---

# Criando um service

```
vagrant@k8s-master:~$ kubectl expose deployment my-nginx
service/my-nginx exposed

```





