## Cấu hình Logstash cho remote server (logstash-forwarder)
Tạo file cấu hình cho input
```
sudo vi /etc/logstash/conf.d/01-lumberjack-input.conf
```
Thêm nội dung sau:
```
input {
  lumberjack {
    port => 5000
    type => "logs"
    ssl_certificate => "/etc/pki/tls/certs/logstash-forwarder.crt"
    ssl_key => "/etc/pki/tls/private/logstash-forwarder.key"
  }
}
```
Tạo file bộ lọc:
```
sudo vi /etc/logstash/conf.d/10-syslog.conf
```
Nội dung:
```
filter {
  if [type] == "syslog" {
    grok {
      match => { "message" => "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?: %{GREEDYDATA:syslog_message}" }
      add_field => [ "received_at", "%{@timestamp}" ]
      add_field => [ "received_from", "%{host}" ]
    }
    syslog_pri { }
    date {
      match => [ "syslog_timestamp", "MMM  d HH:mm:ss", "MMM dd HH:mm:ss" ]
    }
  }
}
```
Tạo file output:
```
sudo vi /etc/logstash/conf.d/30-lumberjack-output.conf
```
Nội dung:
```
output {
  if [type] == "syslog" {
    elasticsearch { host => localhost }
    stdout { codec => rubydebug }
  }
}
```
Khởi động lại **Logstash**
```
sudo service logstash restart
```
