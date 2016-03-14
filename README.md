# kubernetes-zookeeper

## Standalone ZooKeeper example

### 1. Clone kubernetes-zookeeper

```sh
$ cd ${HOME}/git
$ git clone https://github.com/mosuka/kubernetes-zookeeper.git
Cloning into 'kubernetes-zookeeper'...
remote: Counting objects: 50, done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 50 (delta 4), reused 0 (delta 0), pack-reused 41
Unpacking objects: 100% (50/50), done.
Checking connectivity... done.
```

### 2. Start ZooKeeper service

```sh
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-service.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:31294,tcp:31715,tcp:30492) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "zookeeper-service" created
```

### 3. Check ZooKeeper service

```sh
$ kubectl get services -l app=zookeeper-service
NAME                CLUSTER_IP   EXTERNAL_IP   PORT(S)                      SELECTOR            AGE
zookeeper-service   10.3.0.14    nodes         2181/TCP,2888/TCP,3888/TCP   app=zookeeper-pod   3m
```

### 4. Start ZooKeeper controller

```sh
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-controller.yaml
replicationcontroller "zookeeper-controller" created
```

### 5. Check ZooKeeper controller

```sh
$ kubectl get replicationcontrollers -l app=zookeeper-controller
CONTROLLER             CONTAINER(S)          IMAGE(S)                              SELECTOR            REPLICAS   AGE
zookeeper-controller   zookeeper-container   mosuka/docker-zookeeper:release-3.4   app=zookeeper-pod   1          3m
```

### 6. Check ZooKeeper pod

```sh
$ kubectl get pods -l app=zookeeper-pod
NAME                         READY     STATUS    RESTARTS   AGE
zookeeper-controller-3ovgu   1/1       Running   0          4m
```

### 7. Connect to ZooKeeper using zkCli.sh on the local machine

```sh
$ kubectl get pods -l app=zookeeper-pod --template "{{ range .items }}{{if eq .status.phase \"Running\" }}{{ .status.hostIP }}{{ end }}{{ end }}"
172.17.4.201

$ kubectl get services -l app=zookeeper-service --template "{{ range .items }}{{ range .spec.ports }}{{ if eq .name \"client\" }}{{ .nodePort }}{{ end }}{{ end }}{{ end }}"
31294

$ ${HOME}/zookeeper/zookeeper-3.4.8/bin/zkCli.sh -server 172.17.4.201:31294
Connecting to 172.17.4.201:31294
2016-03-14 11:20:06,615 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.8--1, built on 02/06/2016 03:18 GMT
2016-03-14 11:20:06,620 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=172.24.230.16
2016-03-14 11:20:06,620 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.8.0_65
2016-03-14 11:20:06,622 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2016-03-14 11:20:06,622 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_65.jdk/Contents/Home/jre
2016-03-14 11:20:06,622 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../build/classes:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../build/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/slf4j-log4j12-1.6.1.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/slf4j-api-1.6.1.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/netty-3.7.0.Final.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/log4j-1.2.16.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/jline-0.9.94.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../zookeeper-3.4.8.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../src/java/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../conf:
2016-03-14 11:20:06,622 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/Users/mosuka/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
2016-03-14 11:20:06,623 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/var/folders/99/p369dv7n5sqdl9rdvyx0vqzr0000gn/T/
2016-03-14 11:20:06,623 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2016-03-14 11:20:06,623 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Mac OS X
2016-03-14 11:20:06,623 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=x86_64
2016-03-14 11:20:06,623 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=10.11.3
2016-03-14 11:20:06,623 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=mosuka
2016-03-14 11:20:06,624 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/Users/mosuka
2016-03-14 11:20:06,624 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/Users/mosuka/git
2016-03-14 11:20:06,626 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=172.17.4.201:31294 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@446cdf90
Welcome to ZooKeeper!
2016-03-14 11:20:06,657 [myid:] - INFO  [main-SendThread(172.17.4.201:31294):ClientCnxn$SendThread@1032] - Opening socket connection to server 172.17.4.201/172.17.4.201:31294. Will not attempt to authenticate using SASL (unknown error)
JLine support is enabled
2016-03-14 11:20:06,799 [myid:] - INFO  [main-SendThread(172.17.4.201:31294):ClientCnxn$SendThread@876] - Socket connection established to 172.17.4.201/172.17.4.201:31294, initiating session
2016-03-14 11:20:06,815 [myid:] - INFO  [main-SendThread(172.17.4.201:31294):ClientCnxn$SendThread@1299] - Session establishment complete on server 172.17.4.201/172.17.4.201:31294, sessionid = 0x15372eb6f6e0001, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: 172.17.4.201:31294(CONNECTED) 0]
```


## ZooKeeper ensemble example

### 1. Clone kubernetes-zookeeper

```sh
$ cd ${HOME}/git
$ git clone https://github.com/mosuka/kubernetes-zookeeper.git
Cloning into 'kubernetes-zookeeper'...
remote: Counting objects: 50, done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 50 (delta 4), reused 0 (delta 0), pack-reused 41
Unpacking objects: 100% (50/50), done.
Checking connectivity... done.
```

### 2. Start ZooKeeper services

```sh
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-service-1.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:31792,tcp:31032,tcp:31336) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "zookeeper-service-1" created

$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-service-2.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:32140,tcp:31452,tcp:30356) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "zookeeper-service-2" created

$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-service-3.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:32044,tcp:32590,tcp:32133) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "zookeeper-service-3" created
```

### 3. Check ZooKeeper services

```sh
$ kubectl get services -l group=zookeeper-service
NAME                  CLUSTER_IP   EXTERNAL_IP   PORT(S)                      SELECTOR              AGE
zookeeper-service-1   10.3.0.221   nodes         2181/TCP,2888/TCP,3888/TCP   app=zookeeper-pod-1   49s
zookeeper-service-2   10.3.0.4     nodes         2181/TCP,2888/TCP,3888/TCP   app=zookeeper-pod-2   36s
zookeeper-service-3   10.3.0.180   nodes         2181/TCP,2888/TCP,3888/TCP   app=zookeeper-pod-3   32s
```

### 4. Start ZooKeeper controllers

```sh
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-controller-1.yaml
replicationcontroller "zookeeper-controller-1" created

$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-controller-2.yaml
replicationcontroller "zookeeper-controller-2" created

$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.4/zookeeper-controller-3.yaml
replicationcontroller "zookeeper-controller-3" created
```

### 5. Check ZooKeeper controllers

```sh
$ kubectl get replicationcontrollers -l group=zookeeper-controller
CONTROLLER               CONTAINER(S)            IMAGE(S)                              SELECTOR              REPLICAS   AGE
zookeeper-controller-1   zookeeper-container-1   mosuka/docker-zookeeper:release-3.4   app=zookeeper-pod-1   1          23s
zookeeper-controller-2   zookeeper-container-2   mosuka/docker-zookeeper:release-3.4   app=zookeeper-pod-2   1          21s
zookeeper-controller-3   zookeeper-container-3   mosuka/docker-zookeeper:release-3.4   app=zookeeper-pod-3   1          18s
```

### 6. Check ZooKeeper pods

```sh
$ kubectl get pods -l group=zookeeper-pod
NAME                           READY     STATUS    RESTARTS   AGE
zookeeper-controller-1-36moh   1/1       Running   0          1m
zookeeper-controller-2-ogy3p   1/1       Running   0          1m
zookeeper-controller-3-rembk   1/1       Running   0          1m
```

### 7. Connect to ZooKeeper ensemble using zkCli.sh on the local machine

```sh
$ kubectl get pods -l app=zookeeper-pod-1 --template "{{ range .items }}{{if eq .status.phase \"Running\" }}{{ .status.hostIP }}{{ end }}{{ end }}"
172.17.4.201

$ kubectl get services -l app=zookeeper-service-1 --template "{{ range .items }}{{ range .spec.ports }}{{ if eq .name \"client\" }}{{ .nodePort }}{{ end }}{{ end }}{{ end }}"
31639

$ ${HOME}/zookeeper/zookeeper-3.4.8/bin/zkCli.sh -server 172.17.4.201:31639
Connecting to 172.17.4.201:31639
2016-03-14 12:04:39,198 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.8--1, built on 02/06/2016 03:18 GMT
2016-03-14 12:04:39,201 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=172.24.230.16
2016-03-14 12:04:39,201 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.8.0_65
2016-03-14 12:04:39,203 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2016-03-14 12:04:39,203 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_65.jdk/Contents/Home/jre
2016-03-14 12:04:39,203 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../build/classes:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../build/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/slf4j-log4j12-1.6.1.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/slf4j-api-1.6.1.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/netty-3.7.0.Final.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/log4j-1.2.16.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../lib/jline-0.9.94.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../zookeeper-3.4.8.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../src/java/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.4.8/bin/../conf:
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/Users/mosuka/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/var/folders/99/p369dv7n5sqdl9rdvyx0vqzr0000gn/T/
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Mac OS X
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=x86_64
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=10.11.3
2016-03-14 12:04:39,204 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=mosuka
2016-03-14 12:04:39,205 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/Users/mosuka
2016-03-14 12:04:39,205 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/Users/mosuka/git
2016-03-14 12:04:39,206 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=172.17.4.201:31639 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@446cdf90
Welcome to ZooKeeper!
2016-03-14 12:04:39,240 [myid:] - INFO  [main-SendThread(172.17.4.201:31639):ClientCnxn$SendThread@1032] - Opening socket connection to server 172.17.4.201/172.17.4.201:31639. Will not attempt to authenticate using SASL (unknown error)
JLine support is enabled
2016-03-14 12:04:39,380 [myid:] - INFO  [main-SendThread(172.17.4.201:31639):ClientCnxn$SendThread@876] - Socket connection established to 172.17.4.201/172.17.4.201:31639, initiating session
2016-03-14 12:04:39,422 [myid:] - INFO  [main-SendThread(172.17.4.201:31639):ClientCnxn$SendThread@1299] - Session establishment complete on server 172.17.4.201/172.17.4.201:31639, sessionid = 0x153730ce5ab0001, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: 172.17.4.201:31639(CONNECTED) 0]
```



