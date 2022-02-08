# kafka监控

### 一、安装kafka_exporter

#### 1、下载包：

```
wget https://github.com/danielqsj/kafka_exporter/releases/download/v1.2.0/kafka_exporter-1.2.0.linux-amd64.tar.gz
```

#### 2、解压：

```
tar -zxvf kafka_exporter-1.2.0.linux-amd64.tar.gz -C /usr/local/ && mv /usr/local/kafka_exporter-1.2.0.linux-amd64 /usr/local/kafka_exporter
```

#### 3、创建日志文件：

```
touch /var/log/kafka_exporter.log && chmod +777 /var/log/kafka_exporter.log
```

#### 4、编写启动脚本：

```
# touch start_kafka_exporter.sh && chmod +777 start_kafka_exporter.sh

# cat start_kafka_exporter.sh 
#! /bin/bash

/usr/local/kafka_exporter/kafka_exporter --kafka.server=127.0.0.1:9092 >> /var/log/kafka_exporter.log
```

#### 5、创建systemd服务：

```
# cat /usr/lib/systemd/system/kafka_exporter.service

[Unit]
Description=kafka_exporter
Documentation=https://github.com/danielqsj/kafka_exporter/
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
ExecStart=/usr/local/kafka_exporter/start_kafka_exporter.sh

[Install]
WantedBy=multi-user.target
```

#### 6、启动服务：

```
systemctl enable kafka_exporter && systemctl start kafka_exporter && systemctl status kafka_exporter -l
```

#### 7、测试采集指标数：

```
# curl 127.0.0.1:9308/metrics | grep -v '#' | wc -l
```



### 二、配置prometheus拉取kafka指标







### 三、















### 参考资料：

```
1、https://cloud.tencent.com/developer/article/1658936
2、https://blog.csdn.net/x763795151/article/details/119705372
3、https://www.confluent.io/blog/monitor-kafka-clusters-with-prometheus-grafana-and-confluent/
```

