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
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.5/zookeeper-service.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:32256,tcp:30248,tcp:31607) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "zookeeper-service" created
```

### 3. Check ZooKeeper service

```sh
$ kubectl get services -l app=zookeeper-service
NAME                CLUSTER_IP   EXTERNAL_IP   PORT(S)                      SELECTOR            AGE
zookeeper-service   10.3.0.102   nodes         2181/TCP,2888/TCP,3888/TCP   app=zookeeper-pod   24s
```

### 4. Start ZooKeeper controller

```sh
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.5/zookeeper-controller.yaml
replicationcontroller "zookeeper-controller" created
```

### 5. Check ZooKeeper controller

```sh
$ kubectl get replicationcontrollers -l app=zookeeper-controller
CONTROLLER             CONTAINER(S)          IMAGE(S)                              SELECTOR            REPLICAS   AGE
zookeeper-controller   zookeeper-container   mosuka/docker-zookeeper:release-3.5   app=zookeeper-pod   1          12s
```

### 6. Check ZooKeeper pod

```sh
$ kubectl get pods -l app=zookeeper-pod -o wide
NAME                         READY     STATUS    RESTARTS   AGE       NODE
zookeeper-controller-izt90   1/1       Running   0          37s       172.17.4.201
```

### 7. Get host IP and port

```sh
$ ZOOKEEPER_HOST_LIST=($(kubectl get pods -l app=zookeeper-pod --template "{{ range .items }}{{if eq .status.phase \"Running\" }}{{ .status.hostIP }} {{ end }}{{ end }}"))
$ echo ${ZOOKEEPER_HOST_LIST[@]}
172.17.4.201

$ ZOOKEEPER_HOST=${ZOOKEEPER_HOST_LIST[0]}
$ echo ${ZOOKEEPER_HOST}
172.17.4.201

$ ZOOKEEPER_PORT=$(kubectl get services -l app=zookeeper-service --template "{{ range .items }}{{ range .spec.ports }}{{ if eq .name \"client\" }}{{ .nodePort }}{{ end }}{{ end }}{{ end }}")
$ echo ${ZOOKEEPER_PORT}
32256
```

### 8. Connect to ZooKeeper using zkCli.sh on the local machine

```sh
$ ${HOME}/zookeeper/zookeeper-3.5.1-alpha/bin/zkCli.sh -server ${ZOOKEEPER_HOST}:${ZOOKEEPER_PORT} get /zookeeper/config
Connecting to 172.17.4.201:32256
2016-03-22 14:35:00,508 [myid:] - INFO  [main:Environment@109] - Client environment:zookeeper.version=3.5.1-alpha-1693007, built on 07/28/2015 07:19 GMT
2016-03-22 14:35:00,513 [myid:] - INFO  [main:Environment@109] - Client environment:host.name=172.24.231.14
2016-03-22 14:35:00,513 [myid:] - INFO  [main:Environment@109] - Client environment:java.version=1.8.0_65
2016-03-22 14:35:00,517 [myid:] - INFO  [main:Environment@109] - Client environment:java.vendor=Oracle Corporation
2016-03-22 14:35:00,517 [myid:] - INFO  [main:Environment@109] - Client environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_65.jdk/Contents/Home/jre
2016-03-22 14:35:00,517 [myid:] - INFO  [main:Environment@109] - Client environment:java.class.path=/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../build/classes:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../build/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/slf4j-log4j12-1.7.5.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/slf4j-api-1.7.5.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/servlet-api-2.5-20081211.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/netty-3.7.0.Final.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/log4j-1.2.16.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jline-2.11.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jetty-util-6.1.26.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jetty-6.1.26.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/javacc.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jackson-mapper-asl-1.9.11.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jackson-core-asl-1.9.11.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/commons-cli-1.2.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../zookeeper-3.5.1-alpha.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../src/java/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../conf:
2016-03-22 14:35:00,517 [myid:] - INFO  [main:Environment@109] - Client environment:java.library.path=/Users/mosuka/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
2016-03-22 14:35:00,517 [myid:] - INFO  [main:Environment@109] - Client environment:java.io.tmpdir=/var/folders/99/p369dv7n5sqdl9rdvyx0vqzr0000gn/T/
2016-03-22 14:35:00,518 [myid:] - INFO  [main:Environment@109] - Client environment:java.compiler=<NA>
2016-03-22 14:35:00,518 [myid:] - INFO  [main:Environment@109] - Client environment:os.name=Mac OS X
2016-03-22 14:35:00,518 [myid:] - INFO  [main:Environment@109] - Client environment:os.arch=x86_64
2016-03-22 14:35:00,518 [myid:] - INFO  [main:Environment@109] - Client environment:os.version=10.11.3
2016-03-22 14:35:00,518 [myid:] - INFO  [main:Environment@109] - Client environment:user.name=mosuka
2016-03-22 14:35:00,518 [myid:] - INFO  [main:Environment@109] - Client environment:user.home=/Users/mosuka
2016-03-22 14:35:00,519 [myid:] - INFO  [main:Environment@109] - Client environment:user.dir=/Users/mosuka/coreos-kubernetes/multi-node/vagrant
2016-03-22 14:35:00,519 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.free=117MB
2016-03-22 14:35:00,523 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.max=228MB
2016-03-22 14:35:00,524 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.total=123MB
2016-03-22 14:35:00,530 [myid:] - INFO  [main:ZooKeeper@716] - Initiating client connection, connectString=172.17.4.201:32256 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@593634ad
2016-03-22 14:35:00,574 [myid:] - INFO  [main-SendThread(172.17.4.201:32256):ClientCnxn$SendThread@1138] - Opening socket connection to server 172.17.4.201/172.17.4.201:32256. Will not attempt to authenticate using SASL (unknown error)
2016-03-22 14:35:00,694 [myid:] - INFO  [main-SendThread(172.17.4.201:32256):ClientCnxn$SendThread@980] - Socket connection established, initiating session, client: /172.17.4.1:58857, server: 172.17.4.201/172.17.4.201:32256
2016-03-22 14:35:00,841 [myid:] - INFO  [main-SendThread(172.17.4.201:32256):ClientCnxn$SendThread@1400] - Session establishment complete on server 172.17.4.201/172.17.4.201:32256, sessionid = 0x1000129275f0001, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
server.1=10.2.26.5:2888:3888:participant;0.0.0.0:2181
version=100000000
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
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.5/zookeeper-service.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:32256,tcp:30248,tcp:31607) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "zookeeper-service" created
```

### 3. Check ZooKeeper services

```sh
$ kubectl get services -l app=zookeeper-service
NAME                CLUSTER_IP   EXTERNAL_IP   PORT(S)                      SELECTOR            AGE
zookeeper-service   10.3.0.102   nodes         2181/TCP,2888/TCP,3888/TCP   app=zookeeper-pod   23m
```

### 4. Start ZooKeeper controllers

```sh
$ kubectl create -f ${HOME}/git/kubernetes-zookeeper/3.5/zookeeper-ensemble-controller.yaml
replicationcontroller "zookeeper-controller" created
```

### 5. Check ZooKeeper controllers

```sh
$ kubectl get replicationcontrollers -l app=zookeeper-controller
CONTROLLER             CONTAINER(S)          IMAGE(S)                              SELECTOR            REPLICAS   AGE
zookeeper-controller   zookeeper-container   mosuka/docker-zookeeper:release-3.5   app=zookeeper-pod   1          10s
```

### 6. Check ZooKeeper pods

```sh
$ kubectl get pods -l app=zookeeper-pod -o wide
NAME                         READY     STATUS    RESTARTS   AGE       NODE
zookeeper-controller-o94xj   1/1       Running   0          28s       172.17.4.201
```

### 7. Scale ZooKeeper pods

```sh
$ kubectl scale --replicas=2 replicationcontrollers zookeeper-controller
replicationcontroller "zookeeper-controller" scaled

$ kubectl scale --replicas=3 replicationcontrollers zookeeper-controller
replicationcontroller "zookeeper-controller" scaled

$ kubectl get pods -l app=zookeeper-pod -o wide
NAME                         READY     STATUS    RESTARTS   AGE       NODE
zookeeper-controller-li1sa   1/1       Running   0          7s        172.17.4.201
zookeeper-controller-o94xj   1/1       Running   0          2m        172.17.4.201
zookeeper-controller-zbkuz   1/1       Running   0          54s       172.17.4.201
```

### 7. Get host IP and port

```sh
$ ZOOKEEPER_HOST_LIST=($(kubectl get pods -l app=zookeeper-pod --template "{{ range .items }}{{if eq .status.phase \"Running\" }}{{ .status.hostIP }} {{ end }}{{ end }}"))
$ echo ${ZOOKEEPER_HOST_LIST[@]}

$ ZOOKEEPER_1_HOST=${ZOOKEEPER_HOST_LIST[0]}
$ echo ${ZOOKEEPER_1_HOST}
172.17.4.201

$ ZOOKEEPER_2_HOST=${ZOOKEEPER_HOST_LIST[1]}
$ echo ${ZOOKEEPER_2_HOST}
172.17.4.201

$ ZOOKEEPER_3_HOST=${ZOOKEEPER_HOST_LIST[2]}
$ echo ${ZOOKEEPER_3_HOST}
172.17.4.201

$ ZOOKEEPER_PORT=$(kubectl get services -l app=zookeeper-service --template "{{ range .items }}{{ range .spec.ports }}{{ if eq .name \"client\" }}{{ .nodePort }}{{ end }}{{ end }}{{ end }}")
$ echo ${ZOOKEEPER_PORT}
30606
```

### 7. Connect to ZooKeeper ensemble using zkCli.sh on the local machine

```sh
$ ${HOME}/zookeeper/zookeeper-3.5.1-alpha/bin/zkCli.sh -server ${ZOOKEEPER_1_HOST}:${ZOOKEEPER_PORT} get /zookeeper/config
Connecting to 172.17.4.201:31836
2016-03-23 17:41:12,044 [myid:] - INFO  [main:Environment@109] - Client environment:zookeeper.version=3.5.1-alpha-1693007, built on 07/28/2015 07:19 GMT
2016-03-23 17:41:12,050 [myid:] - INFO  [main:Environment@109] - Client environment:host.name=192.168.99.1
2016-03-23 17:41:12,051 [myid:] - INFO  [main:Environment@109] - Client environment:java.version=1.8.0_65
2016-03-23 17:41:12,054 [myid:] - INFO  [main:Environment@109] - Client environment:java.vendor=Oracle Corporation
2016-03-23 17:41:12,055 [myid:] - INFO  [main:Environment@109] - Client environment:java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_65.jdk/Contents/Home/jre
2016-03-23 17:41:12,055 [myid:] - INFO  [main:Environment@109] - Client environment:java.class.path=/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../build/classes:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../build/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/slf4j-log4j12-1.7.5.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/slf4j-api-1.7.5.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/servlet-api-2.5-20081211.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/netty-3.7.0.Final.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/log4j-1.2.16.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jline-2.11.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jetty-util-6.1.26.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jetty-6.1.26.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/javacc.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jackson-mapper-asl-1.9.11.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/jackson-core-asl-1.9.11.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../lib/commons-cli-1.2.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../zookeeper-3.5.1-alpha.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../src/java/lib/*.jar:/Users/mosuka/zookeeper/zookeeper-3.5.1-alpha/bin/../conf:
2016-03-23 17:41:12,055 [myid:] - INFO  [main:Environment@109] - Client environment:java.library.path=/Users/mosuka/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
2016-03-23 17:41:12,055 [myid:] - INFO  [main:Environment@109] - Client environment:java.io.tmpdir=/var/folders/99/p369dv7n5sqdl9rdvyx0vqzr0000gn/T/
2016-03-23 17:41:12,055 [myid:] - INFO  [main:Environment@109] - Client environment:java.compiler=<NA>
2016-03-23 17:41:12,056 [myid:] - INFO  [main:Environment@109] - Client environment:os.name=Mac OS X
2016-03-23 17:41:12,056 [myid:] - INFO  [main:Environment@109] - Client environment:os.arch=x86_64
2016-03-23 17:41:12,056 [myid:] - INFO  [main:Environment@109] - Client environment:os.version=10.11.4
2016-03-23 17:41:12,056 [myid:] - INFO  [main:Environment@109] - Client environment:user.name=mosuka
2016-03-23 17:41:12,056 [myid:] - INFO  [main:Environment@109] - Client environment:user.home=/Users/mosuka
2016-03-23 17:41:12,056 [myid:] - INFO  [main:Environment@109] - Client environment:user.dir=/Users/mosuka
2016-03-23 17:41:12,057 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.free=117MB
2016-03-23 17:41:12,059 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.max=228MB
2016-03-23 17:41:12,059 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.total=123MB
2016-03-23 17:41:12,064 [myid:] - INFO  [main:ZooKeeper@716] - Initiating client connection, connectString=172.17.4.201:31836 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@593634ad
2016-03-23 17:41:12,121 [myid:] - INFO  [main-SendThread(172.17.4.201:31836):ClientCnxn$SendThread@1138] - Opening socket connection to server 172.17.4.201/172.17.4.201:31836. Will not attempt to authenticate using SASL (unknown error)
2016-03-23 17:41:12,282 [myid:] - INFO  [main-SendThread(172.17.4.201:31836):ClientCnxn$SendThread@980] - Socket connection established, initiating session, client: /172.17.4.1:54623, server: 172.17.4.201/172.17.4.201:31836
2016-03-23 17:41:12,311 [myid:] - INFO  [main-SendThread(172.17.4.201:31836):ClientCnxn$SendThread@1400] - Session establishment complete on server 172.17.4.201/172.17.4.201:31836, sessionid = 0x20000111c540002, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
server.1=10.2.22.3:2888:3888:participant;0.0.0.0:2181
server.2=10.2.22.2:2888:3888:participant;0.0.0.0:2181
server.3=10.2.22.5:2888:3888:participant;0.0.0.0:2181
version=200000003
```



