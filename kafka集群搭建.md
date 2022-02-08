# kafka集群搭建

### 一、部署JDK：

#### 1、安装：

```
# yum install java-1.8.0-openjdk -y

# java -version
openjdk version "1.8.0_312"
OpenJDK Runtime Environment (build 1.8.0_312-b07)
OpenJDK 64-Bit Server VM (build 25.312-b07, mixed mode)
```

### 二、部署ZooKeeper：

#### 1、创建目录：

```
#创建应用目录
mkdir -p /usr/local/zookeeper

#创建数据目录
mkdir -p /data/zookeeper/
mkdir -p /data/zookeeper/data
mkdir -p /data/zookeeper/logs
```

#### 2、解压到应用目录：

```
# 下载
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.5.9/apache-zookeeper-3.5.9-bin.tar.gz

# 解压
tar -zxvf apache-zookeeper-3.5.9-bin.tar.gz -C /usr/local/zookeeper/
```

#### 3、配置环境变量：

```
#追加以下内容
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$ZOOKEEPER_HOME/bin:$PATH

#使环境变量生效
source /etc/profile

#查看配置结果
echo $ZOOKEEPER_HOME
```

#### 4、新建配置文件zoo.cfg：

```
# cat /usr/local/zookeeper/conf/zoo.cfg 

tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data/zookeeper/data
dataLogDir=/data/zookeeper/logs
clientPort=2181
server.1=192.168.133.140:2888:3888
server.2=192.168.133.137:2888:3888
server.3=192.168.133.138:2888:3888
```

#### 5、配置节点标识：

```
# 三个节点分别标识为1、2、3
# 第一个节点
# cat /data/zookeeper/data/myid 
1
```

#### 6、启动ZooKeeper：

```
# sh /usr/local/zookeeper/bin/zkServer.sh start
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

# sh /usr/local/zookeeper/bin/zkServer.sh status
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
```

#### 7、连接其它节点：

```
# sh /usr/local/zookeeper/bin/zkCli.sh -server 192.168.133.137:2181
/usr/bin/java
Connecting to 192.168.133.137:2181
2022-01-18 09:27:29,749 [myid:] - INFO  [main:Environment@109] - Client environment:zookeeper.version=3.5.9-83df9301aa5c2a5d284a9940177808c01bc35cef, built on 01/06/2021 19:49 GMT
2022-01-18 09:27:29,752 [myid:] - INFO  [main:Environment@109] - Client environment:host.name=kafka-zk-ch-1
2022-01-18 09:27:29,752 [myid:] - INFO  [main:Environment@109] - Client environment:java.version=1.8.0_312
2022-01-18 09:27:29,755 [myid:] - INFO  [main:Environment@109] - Client environment:java.vendor=Red Hat, Inc.
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:java.home=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64/jre
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:java.class.path=/usr/local/zookeeper/bin/../zookeeper-server/target/classes:/usr/local/zookeeper/bin/../build/classes:/usr/local/zookeeper/bin/../zookeeper-server/target/lib/*.jar:/usr/local/zookeeper/bin/../build/lib/*.jar:/usr/local/zookeeper/bin/../lib/zookeeper-jute-3.5.9.jar:/usr/local/zookeeper/bin/../lib/zookeeper-3.5.9.jar:/usr/local/zookeeper/bin/../lib/slf4j-log4j12-1.7.25.jar:/usr/local/zookeeper/bin/../lib/slf4j-api-1.7.25.jar:/usr/local/zookeeper/bin/../lib/netty-transport-native-unix-common-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-transport-native-epoll-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-transport-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-resolver-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-handler-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-common-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-codec-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/netty-buffer-4.1.50.Final.jar:/usr/local/zookeeper/bin/../lib/log4j-1.2.17.jar:/usr/local/zookeeper/bin/../lib/json-simple-1.1.1.jar:/usr/local/zookeeper/bin/../lib/jline-2.14.6.jar:/usr/local/zookeeper/bin/../lib/jetty-util-ajax-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/jetty-util-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/jetty-servlet-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/jetty-server-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/jetty-security-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/jetty-io-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/jetty-http-9.4.35.v20201120.jar:/usr/local/zookeeper/bin/../lib/javax.servlet-api-3.1.0.jar:/usr/local/zookeeper/bin/../lib/jackson-databind-2.10.5.1.jar:/usr/local/zookeeper/bin/../lib/jackson-core-2.10.5.jar:/usr/local/zookeeper/bin/../lib/jackson-annotations-2.10.5.jar:/usr/local/zookeeper/bin/../lib/commons-cli-1.2.jar:/usr/local/zookeeper/bin/../lib/audience-annotations-0.5.0.jar:/usr/local/zookeeper/bin/../zookeeper-*.jar:/usr/local/zookeeper/bin/../zookeeper-server/src/main/resources/lib/*.jar:/usr/local/zookeeper/bin/../conf:
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:java.io.tmpdir=/tmp
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:java.compiler=<NA>
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:os.name=Linux
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:os.arch=amd64
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:os.version=3.10.0-1160.49.1.el7.x86_64
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:user.name=root
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:user.home=/root
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:user.dir=/usr/local/zookeeper/bin
2022-01-18 09:27:29,756 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.free=23MB
2022-01-18 09:27:29,758 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.max=228MB
2022-01-18 09:27:29,758 [myid:] - INFO  [main:Environment@109] - Client environment:os.memory.total=29MB
2022-01-18 09:27:29,764 [myid:] - INFO  [main:ZooKeeper@868] - Initiating client connection, connectString=192.168.133.137:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@4cdf35a9
2022-01-18 09:27:29,771 [myid:] - INFO  [main:X509Util@79] - Setting -D jdk.tls.rejectClientInitiatedRenegotiation=true to disable client-initiated TLS renegotiation
2022-01-18 09:27:29,780 [myid:] - INFO  [main:ClientCnxnSocket@237] - jute.maxbuffer value is 4194304 Bytes
2022-01-18 09:27:29,791 [myid:] - INFO  [main:ClientCnxn@1653] - zookeeper.request.timeout value is 0. feature enabled=
Welcome to ZooKeeper!
JLine support is enabled
2022-01-18 09:27:29,813 [myid:192.168.133.137:2181] - INFO  [main-SendThread(192.168.133.137:2181):ClientCnxn$SendThread@1112] - Opening socket connection to server kafka-zk-ch-2/192.168.133.137:2181. Will not attempt to authenticate using SASL (unknown error)
2022-01-18 09:27:29,882 [myid:192.168.133.137:2181] - INFO  [main-SendThread(192.168.133.137:2181):ClientCnxn$SendThread@959] - Socket connection established, initiating session, client: /192.168.133.140:40104, server: kafka-zk-ch-2/192.168.133.137:2181
2022-01-18 09:27:29,906 [myid:192.168.133.137:2181] - INFO  [main-SendThread(192.168.133.137:2181):ClientCnxn$SendThread@1394] - Session establishment complete on server kafka-zk-ch-2/192.168.133.137:2181, sessionid = 0x2000004e3da0001, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: 192.168.133.137:2181(CONNECTED) 0]
```

#### 8、查询ZK的根：

```
[zk: 192.168.133.137:2181(CONNECTED) 1] ls /
[zookeeper]
```

#### 9、创建systemd服务：

```
# cat zookeeper.service 
[Unit]
Description=zookeeper
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/zookeeper/bin/zkServer.sh start
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### 三、部署kafka：

#### 1、安装：

```
# 下载
wget https://archive.apache.org/dist/kafka/2.8.0/kafka_2.12-2.8.0.tgz

# 解压
tar -xzvf kafka_2.12-2.8.0.tgz -C /usr/local/kafka
```

#### 2、通用配置（kafka集群三节点均需配置）：

```
# 配置日志目录
# cat /usr/local/kafka/config/server.properties | grep logs
log.dirs=/usr/local/kafka/logs

# 指定ZK服务器
# cat /usr/local/kafka/config/server.properties | grep zookeeper
zookeeper.connect=192.168.133.140:2181,192.168.133.137:2181,192.168.133.138:2181
```

#### 3、分节点配置：

```
# kafka-1设置broker.id = 0，其它两个节点分别为1、2
# cat /usr/local/kafka/config/server.properties | grep broker.id
broker.id=0

# 监听本地9092端口
# cat /usr/local/kafka/config/server.properties | grep 9092
# listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://192.168.133.140:9092
```

#### 4、启动kafka：

```
# /usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties &
[1] 3870
...
[2022-01-18 10:34:52,000] INFO Kafka version: 2.8.0 (org.apache.kafka.common.utils.AppInfoParser)
[2022-01-18 10:34:52,000] INFO Kafka commitId: ebb1d6e21cc92130 (org.apache.kafka.common.utils.AppInfoParser)
[2022-01-18 10:34:52,000] INFO Kafka startTimeMs: 1642473291987 (org.apache.kafka.common.utils.AppInfoParser)
[2022-01-18 10:34:52,001] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
[2022-01-18 10:34:52,070] INFO [broker-0-to-controller-send-thread]: Recorded new controller, from now on will use broker 192.168.133.140:9092 (id: 0 rack: null) (kafka.server.BrokerToControllerRequestThread)
[2022-01-18 10:34:52,100] INFO [Partition test-ken-io-0 broker=0] Log loaded for partition test-ken-io-0 with initial high watermark 1 (kafka.cluster.Partition)
```

### 四、测试kafka：

#### 1、创建topic：

```
# 在kafka01(Broker)上创建测试Tpoic：test-ken-io，并指定3个副本、1个分区
# /usr/local/kafka/bin/kafka-topics.sh --create --bootstrap-server 192.168.133.140:9092 --replication-factor 3 --partitions 1 --topic test-ken-io
Created topic test-ken-io.
```

#### 2、查看topic：

```
# /usr/local/kafka/bin/kafka-topics.sh --list --bootstrap-server 192.168.133.140:9092 
test-ken-io
# 
# /usr/local/kafka/bin/kafka-topics.sh --list --bootstrap-server 192.168.133.137:9092 
test-ken-io
# 
# /usr/local/kafka/bin/kafka-topics.sh --list --bootstrap-server 192.168.133.138:9092 
test-ken-io
```

#### 3、发生消息：

```
# 向Broker(id=0)的Topic=test-ken-io发送消息
# /usr/local/kafka/bin/kafka-console-producer.sh --broker-list  192.168.133.140:9092  --topic test-ken-io
>test by ken.io
```

#### 4、消费消息：

```
# 在kafka-2、kafka-3节点上均可消费Broker1的消息
# /usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server 192.168.133.140:9092  --topic test-ken-io --from-beginning
test
test by ken.io
hello
ni
```

#### 5、删除topic：

```
# /usr/local/kafka/bin/kafka-topics.sh --delete --bootstrap-server 192.168.133.140:9092 --topic test-ken-io
```

#### 6、创建systemd服务：

```
# cat kafka.service 
[Unit]
Description=kafka
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties
Restart=on-failure

[Install]
WantedBy=multi-user.target
```





### 参考资料：

```
1、https://www.cnblogs.com/kentalk/p/kafka-cluster-deploy-guide.html
2、https://ken.io/note/zookeeper-cluster-deploy-guide
3、https://segmentfault.com/a/1190000041186974
```

