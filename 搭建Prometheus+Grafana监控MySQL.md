# 监控MySQL数据库

### 一、环境准备：

#### 1、关闭firewalld：

```
systemctl stop firewalld  &&  systemctl disable firewalld && sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```

### 二、安装Prometheus：

#### 1、下载Prometheus安装包：

```
wget https://github.com/prometheus/prometheus/releases/download/v2.25.0/prometheus-2.25.0.linux-amd64.tar.gz
```

#### 2、解压：

```
tar -xzvf prometheus-2.25.0.linux-amd64.tar.gz
```

#### 3、查看版本：

```
./prometheus --version
```

#### 4、启动：

```
./prometheus --config.file=prometheus.yml
```

#### 5、配置开机自启动：

##### 1）修改目录并更改目录用户组：

```
mv prometheus-2.25.0.linux-amd64 /usr/local/prometheus
useradd prometheus
chown -R prometheus:prometheus /usr/local/prometheus
```

##### 2）创建systemd服务：

```
# cat /usr/lib/systemd/system/prometheus.service

[Unit]
Description=prometheus
After=network.target

[Service]
Type=simple
User=prometheus
ExecStart=/usr/local/prometheus/prometheus --config.file=/usr/local/prometheus/prometheus.yml --storage.tsdb.path=/usr/local/prometheus/data
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

#### 6、启动服务：

```
systemctl daemon-reload && systemctl start prometheus && systemctl status prometheus && systemctl enable prometheus
```

#### 7、在浏览器输入ip:9090打开页面。



### 三、安装Grafana：

#### 1、下载Grafana包：

```
wget https://dl.grafana.com/oss/release/grafana-7.4.0-1.x86_64.rpm
```

#### 2、安装：

```
yum install grafana-7.4.0-1.x86_64.rpm
```

#### 3、启动：

```
systemctl daemon-reload && systemctl start grafana-server  &&  systemctl enable grafana-server && systemctl status grafana-server
```

#### 4、在浏览器输入ip:3000打开页面。



### 四、添加主机监控：

#### 1、下载node_exporter：

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.1.1/node_exporter-1.1.1.linux-amd64.tar.gz
```

#### 2、解压：

```
tar -xzvf node_exporter-1.1.1.linux-amd64.tar.gz
```

#### 3、进入node_exporter目录运行node_exporter：

```
cd node_exporter-1.1.1.linux-amd64 && ./node_exporter
```

#### 4、查看输出结果：

```
curl http://localhost:9100/metrics
```

#### 5、将监控添加进Prometheus，在prometheus.yml的scrape_configs模块中追加：

##### 1）本地主机：

```
- job_name: 'server'
    static_configs:
      - targets: ['localhost:9100']
```

##### 2）远程主机：

```
- job_name: 'server2'
    static_configs:
      - targets: ['192.168.182.230:9100']
```

#### 6、创建服务开机自启动：

##### 1）修改目录并更改目录用户组：

```
mv node_exporter-1.1.1.linux-amd64  /usr/local/node_exporter
useradd prometheus
chown -R prometheus:prometheus /usr/local/node_exporter
```

##### 2）创建systemd服务：

```
# cat /usr/lib/systemd/system/node_exporter.service

[Unit]
Description=node_exporter
After=network.target

[Service]
Type=simple
User=prometheus
ExecStart=/usr/local/node_exporter/node_exporter
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

##### 3）启动服务：

```
systemctl start node_exporter && systemctl status node_exporter && systemctl enable node_exporter
```



### 五、添加数据库监控：

#### 1、下载mysqld_exporter：

```
wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.12.1/mysqld_exporter-0.12.1.linux-amd64.tar.gz
```

#### 2、解压：

```
tar -xzvf mysqld_exporter-0.12.1.linux-amd64.tar.gz
```

####  3、在MySQL数据库创建监控账号并授权：

```
GRANT REPLICATION CLIENT,PROCESS ON *.* TO 'prometheus'@'localhost' identified by '12a4B56!';

GRANT SELECT ON *.* TO 'prometheus'@'localhost';
flush privileges;
```

#### 4、创建配置文件：

##### 1）编写配置文件：

```
# cat /usr/local/mysqld_exporter/.my.cnf 
[client]

host=127.0.0.1
port=3306
user=prometheus
password=12a4B56!
```

#### 5、启动：

```
./mysqld_exporter --config.my-cnf=.my.cnf
```

#### 6、创建开机自启动：

##### 1）修改目录并更改目录用户组：

```
mv mysqld_exporter-0.12.1.linux-amd64 /usr/local/mysqld_exporter
useradd prometheus
chown -R prometheus:prometheus /usr/local/mysqld_exporter
```

##### 2）创建systemd服务：

```
# cat /usr/lib/systemd/system/mysqld_exporter.service

[Service]
Type=simple
User=prometheus
Restart=on-failure
ExecStart=/usr/local/mysqld_exporter/mysqld_exporter --config.my-cnf=/usr/local/mysqld_exporter/.my.cnf \
--collect.global_status \
--collect.info_schema.innodb_metrics \
--collect.auto_increment.columns \
--collect.info_schema.processlist \
--collect.binlog_size \
--collect.info_schema.tablestats \
--collect.global_variables \
--collect.info_schema.query_response_time \
--collect.info_schema.userstats \
--collect.info_schema.tables \
--collect.perf_schema.tablelocks \
--collect.perf_schema.file_events \
--collect.perf_schema.eventswaits \
--collect.perf_schema.indexiowaits \
--collect.perf_schema.tableiowaits \
--collect.slave_status \
--web.listen-address=0.0.0.0:9104

[Install]
WantedBy=multi-user.target
```

##### 3）启动服务：

```
systemctl start mysqld_exporter && systemctl status mysqld_exporter && systemctl enable mysqld_exporter
```

##### 4）测试：

```
1、curl 127.0.0.1:9104/metrics | wc -l
2、curl http://localhost:9104/metrics | grep mysql_up 是否为1
```

#### 7、在prometheus.yml文件的scrape_configs模块中追加：

```
- job_name: 'mysql'
    static_configs:
      - targets: ['192.168.182.230:9104']
```

#### 8、重启promethues

#### 9、在grafana中导入魔板（7362）和数据源（Prometheus），可查看。





### 参考资料：

```
1、https://grafana.com/grafana/download/7.4.0
2、https://www.jianshu.com/p/8d2c020313f0
3、https://blog.csdn.net/qq292913477/article/details/85625242
4、https://www.sohu.com/a/383302760_411876
```

