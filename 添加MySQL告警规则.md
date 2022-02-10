### 一、prometheus：

#### 1、prometheus.yaml：

```
# cat prometheus.yml

global:
  scrape_interval: 120s 
  evaluation_interval: 120s 

alerting:
  alertmanagers:
  - static_configs:
    - targets: ['10.129.137.229:9098']

rule_files:
   - "/usr/local/prometheus/rules/mysql_rules.yml"

scrape_configs:

  - job_name: '192.168.44.66'
    static_configs:
    - targets: ['192.168.44.66:9104','192.168.44.66:9100']
      labels:
          instance: '192.168.44.66'

  - job_name: '192.168.44.67'
    static_configs:
    - targets: ['192.168.44.67:9104','192.168.44.67:9100']
      labels:
          instance: '192.168.44.67'
```

#### 2、mysql告警规则：

```
# cat /usr/local/prometheus/rules/mysql_rules.yml

groups:
- name: MySQL_Status
  rules:
  - alert: MySQL_Down 
    expr: mysql_up == 0
    for: 5s 
    labels:
      severity: "【高】"
    annotations:
      summary: "{{$labels.instance}}: 数据库宕机！请立即排查！"
      description: "EPC数据库集群监控"

  - alert: MySQL_Restart
    expr: mysql_global_status_uptime < 600
    for: 1m
    labels:
      severity: "【高】"
    annotations:
      summary: "{{$labels.instance}}: 数据库重启！请立即排查！"
      description: "EPC数据库集群监控"

  - alert: Galera_Node_Is_Not_Ready
    expr: mysql_global_status_wsrep_ready != 1
    for: 5m
    labels:
      severity: "【高】"
    annotations:
      summary: "{{$labels.instance}}: 数据库集群状态异常！请立即排查！"
      description: "EPC数据库集群监控"

  - alert: InnoDB_Log_Writes_Are_Stalling
    expr: rate(mysql_global_status_innodb_log_waits[15m]) > 10
    for: 5m
    labels: 
      severity: "【高】" 
    annotations:
      summary: "{{$labels.instance}}: 数据库集群异常！请立即排查！"
      description: "EPC数据库集群监控"

  - alert: Mysql_Too_Many_Connections
    expr: mysql_global_status_max_used_connections > mysql_global_variables_max_connections * 0.8
    for: 5s
    labels:
      severity: "【高】"
    annotations:
      summary: "{{$labels.instance}}: 连接数过多，使用超过80%连接限制。请处理，现在连接数是：{{ $value }}"
      description: "EPC数据库集群监控"

  - alert: Mysql_Too_Many_Slow_Queries
    expr: rate(mysql_global_status_slow_queries[5m]) > 1
    for: 2m
    labels:
      severity: "【高】"
    annotations:
      summary: "{{$labels.instance}}: 慢查询较多，请检查处理。当前慢查询数量是: {{ $value }}"
      description: "EPC数据库集群监控"
```

### 三、alertmanager：

#### 1、alertmanager.yml 

```
# cat alertmanager.yml 

global:
  resolve_timeout: 1m
  wechat_api_corp_id: 'ww09483d40c40973c6'
  wechat_api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'
  wechat_api_secret: 'hJifBnTdktpHjZSET3dh2IGjJ1g815-Unh3Cc70nStI'

templates:
  - '/usr/local/alertmanager/template/mysql.tmpl'

route:
  group_by: ['job','alertname']
  group_wait: 10s
  group_interval: 20s
  repeat_interval: 1m
  receiver: 'wechat'

receivers:
- name: 'wechat'
  wechat_configs:
  - send_resolved: true
    to_party: '1'
    agent_id: 1000002
    corp_id: 'ww09483d40c40973c6'
    api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'
    api_secret: 'hJifBnTdktpHjZSET3dh2IGjJ1g815-Unh3Cc70nStI'
```

#### 2、mysql告警魔板：

```
# cat /usr/local/alertmanager/template/mysql.tmpl

{{ define "wechat.default.message" }}
{{- if gt (len .Alerts.Firing) 0 -}}{{ range .Alerts }}
[!!!!!故障告警!!!!!]
资源类型: {{ .Annotations.description }}
告警地址: {{ .Labels.instance }}
告警详情: {{ .Annotations.summary }}
告警级别: {{ .Labels.severity }}
故障时间: {{ .StartsAt.Local.Format "2006-01-02 15:04:05" }}
[**********end*********]
{{ end }}{{ end -}}
{{- if gt (len .Alerts.Resolved) 0 -}}{{ range .Alerts }}
[+++故障恢复通知+++]
资源类型: {{ .Annotations.description }}
告警地址: {{ .Labels.instance }}
告警详情: {{ .Annotations.summary }}
告警级别: {{ .Labels.severity }}
故障时间: {{ .StartsAt.Local.Format "2006-01-02 15:04:05" }}
恢复时间: {{ .EndsAt.Local.Format "2006-01-02 15:04:05" }}
[*********end**********]
{{ end }}{{ end -}}
{{- end }}
```

