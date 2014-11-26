# Logstash forwarder
Là dịch vụ cho phép chuyển tiếp dữ liệu log từ một máy tính (**server**) đến một server trung (**Logstash-server**) tâm cài đặt Logstash để xử lý và quản lý.
<br/>
***Chú ý:** Bài hướng dẫn sau dành cho CentOS (based RedHat).

## 1. Cài đặt
Trên **Logstash-server** (Server cài đặt ELK):
<br/>
Sinh chứng chỉ SSL:
```
sudo mkdir -p /etc/pki/tls/certs
sudo mkdir /etc/pki/tls/private
cd /etc/pki/tls;
sudo openssl req -x509 -batch -nodes -days 3650 -newkey rsa:2048 -keyout private/logstash-forwarder.key -out certs/logstash-forwarder.crt
```
Copy file `*.cert` đến **Server** cài đặt **Logstash-forwarder** (làm điều này trên mỗi Server)
```
scp /etc/pki/tls/certs/logstash-forwarder.crt user@server_private_IP:/tmp
```

Trong đó __user__ và **server\_private\_ID** đuợc thay thế tuơng ứng với user và đỉa chị ip thực tế.


Trên **Server** (cài Logstash forwarder)
Tải về file cài đặt
```
cd ~;
curl -O http://packages.elasticsearch.org/logstashforwarder/centos/logstash-forwarder-0.3.1-1.x86_64.rpm
```
Cài đặt

```
sudo rpm -ivh ~/logstash-forwarder-0.3.1-1.x86_64.rpm
```
Bạn sẽ thay tên phiên bản tuơng ứng phù hợp với máy của bạn.

Để Logstash forwarder tự động chạy khi khởi động:
```
cd /etc/init.d/;
sudo curl -o logstash-forwarder http://logstashbook.com/code/4/logstash_forwarder_redhat_init
sudo chmod +x logstash-forwarder
```
```
sudo curl -o /etc/sysconfig/logstash-forwarder http://logstashbook.com/code/4/logstash_forwarder_redhat_sysconfig
```
Sửa file `sudo vi /etc/sysconfig/logstash-forwarder`,
thay `LOGSTASH_FORWARDER_OPTIONS` thành như sau
```
LOGSTASH_FORWARDER_OPTIONS="-config /etc/logstash-forwarder -spool-size 100"
```
Copy chứng chỉ SSL vào thư mục phù hợp
```
sudo mkdir -p /etc/pki/tls/certs
sudo cp /tmp/logstash-forwarder.crt /etc/pki/tls/certs/
```

## 2. Cấu hình
Trên mỗi **Server** (cài đặt **Logstash-forwarder**):

Tạo file cấu hình cho **Logtash-forwarder**:

```
sudo vi /etc/logstash-forwarder
```

Thêm nội dung vào như sau:
```
{
  "network": {
    "servers": [ "logstash_server_private_IP:5000" ],
    "timeout": 15,
    "ssl ca": "/etc/pki/tls/certs/logstash-forwarder.crt"
  },
  "files": [
    {
      "paths": [
        "/var/log/messages",
        "/var/log/secure"
       ],
      "fields": { "type": "syslog" }
    }
   ]
}
```
Với **logstash\_server\_private\_IP** đuợc thay bằng IP của **Logstash-Server**, **_5000_** chính là cổng lắng nghe **Logtash-forwarder** tại **Logtash-Server**.
<br/>
File cấu hình đuợc tổ chức theo JSON, đầu tiên là đối tượng **network**, sau đó là các *input* đầu vào cho **Logstash** trên **Logstash-server**.
Ở đây ta có 2 file đầu vào đuợc chỉ định trong trường *paths*, kiểu đầu vào đuợc chỉ định trong trường *fields*.

Thêm **Logstash-forwarder** vào *chkconfig*
```
sudo chkconfig --add logstash-forwarder
```
Khởi động dịch vụ:
```
sudo service logstash-forwarder start
```

## 3. Cấu hình Logstash cho remote server
Ở **Logstash-server**:
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
