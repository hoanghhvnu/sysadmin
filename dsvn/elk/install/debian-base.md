# Cài đặt ELK thông qua repositories trên Debian/Ubuntu
## 1. Cài đặt ElasticSearch
Phiên bản mới nhất của **Logstash** là `1.4.2`, được khuyến khích dùng với ElasticSearch `1.1.1` (hiện nay đã có phiên bản mới hơn).

Thêm khóa công khai của ElasticSearch vào apt:
```
wget -O - http://packages.elasticsearch.org/GPG-KEY-elasticsearch | sudo apt-key add -
```
Tạo file chứa danh sách các nguồn cho ElasticSearch
```
echo 'deb http://packages.elasticsearch.org/elasticsearch/1.1/debian stable main' | sudo tee /etc/apt/sources.list.d/elasticsearch.list
```
Cập nhật cơ sở dữ liệu gói **apt**
```
sudo apt-get update
```
Cài đặt **ElasticSearch**
```
sudo apt-get -y install elasticsearch=1.1.1
```
Ở đây cài phiên bản `1.1.1`, thay thế nó nếu bạn muốn cài phiên bản khác. Để xem danh sách các phiên bản đang có, dùng lệnh
`apt-cache policy elasticsearch`

Khởi động dịch vụ **ElasticSearch**
```
sudo service elasticsearch start
```

Để **ElasticSearch** chạy khi khởi động, chạy
```
sudo update-rc.d elasticsearch defaults 95 10
```

## 2. Cài đặt Logstash
Tương tự như cài đặt ElasticSearch, ta làm lần lượt:
```
echo 'deb http://packages.elasticsearch.org/logstash/1.4/debian stable main' | sudo tee /etc/apt/sources.list.d/logstash.list
```
```
sudo apt-get update
```
```
sudo apt-get install logstash=1.4.2-1-2c0f5a1
```
Ở đây ta cài bản 1.4.2, chú ý rằng bạn sẽ thay thế chuỗi phía sau dấu `=` bằng nếu muốn dùng phiên bản khác.

Khởi động **logstash**
```
sudo service logstash start
```

## 3. Cài đặt Kibana
Tải về **Kibana** từ [đây](http://www.elasticsearch.org/overview/elkdownloads/)
<br/>
Để có thể chạy được trên trình duyệt, **Kibana** cần được chạy trên một server, ví dụ **Apache**, **Nginx**,...

###3.1. Nginx server

```
sudo mkdir -p /srv/www/kibana
sudo tar xf kibana-3.1.0.tar.gz -C /srv/www/  
sudo chown -R www-data:www-data /srv/www/  
```
Ở đây dùng phiên bản `3.1.0`, bạn sẽ thay `3.1.0` bằng tên phiên bản Kibana mà bạn dùng.

#####Cấu hình nginx
<br/>
Sửa file `/etc/nginx/sites-available/default` thành như sau:
<br/>(Nhớ sao lưu file gốc để có thể dùng lại nếu cần)
```
server {  
        listen 80 default_server;

        root /srv/www;
        index index.html index.htm;

        # Make site accessible from http://localhost/
        server_name localhost;

        location / {
                try_files $uri $uri/ =404;
        }
        location /kibana {
                alias /srv/www/kibana-3.1.0/;
                try_files $uri $uri/ =404;
        }
}
```

Tải lại cấu hình cho **nginx** bằng lệnh `sudo service nginx reload`
Để truy cập **Kibana**, chạy trên trình duyệt: `http://localhost/kibana`
###3.2. Trên **apache** server
Chỉ cần giải nén vào thư mục `/var/www/kibana`
```
sudo mkdir -p /var/www/kibana
sudo tar xf kibana-3.1.0.tar.gz -C /var/www/kibana
```
