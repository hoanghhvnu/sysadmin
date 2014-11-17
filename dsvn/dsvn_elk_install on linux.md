# Cài đặt ELK trên linux
**ElasticSearch** là một công cụ mã nguồn mở cho phép tìm kiếm và phân tích dữ liệu.<br/>
**Logstash** là một công cụ giúp phân tích log và xuất ra định dạng thông dụng ví dụ JSON.<br/>
**Kibana** là công nghệ giúp trực quan hóa dữ liệu của ElasticSearch

## Cài đặt java
**Logstash** cần có **java** mới có thể chạy được.
Nếu chưa cài **java**, làm theo hướng dẫn sau.
1. **Tải về**
<br/>
Vào trang chủ [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html),
 nhấn vào *__Accept License Agreement__* và chọn phiên bản Java phù hợp với máy tính của mình và tải về.
2. **Giải nén**
<br/>
Giả sử phiên bản của tôi là java7 update 5, linux 64-bit. Tên file tải về là `jdk-7u5-linux-x64.tar.gz` (bạn sẽ thay bằng tên file của bạn)
```
$ tar xf jdk-7u5-linux-x64.tar.gz
$ sudo mkdir /usr/lib/jvm/
$ sudo mv ./jdk1.7.0_05 /usr/lib/jvm/jdk1.7.0_05
```
3. **Cài đặt**
```
$ sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.7.0_05/bin/java" 1
$ sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk1.7.0_05/bin/javac" 1
$ sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/jvm/jdk1.7.0_05/bin/javaws" 1
```
4. **Cài java làm mặc định**
```
$ sudo update-alternatives --config java # (chọn số tương ứng với đường dẫn)
$ sudo update-alternatives --config javac
$ sudo update-alternatives --config javaws
```
5. **Kiểm tra**
<br/>
Chạy `java -version`, nếu kết quả trả về là phiên bản java thì bạn đã cài đặt thành công.

_**Nguồn tham khảo**_<br/>
**Wikilinux** [link](http://wikilinux.vn/cai-dat-oracle-jdk-7-cho-linux/)
## Cài đặt ELK
Tải Elasticsearch, Logstash, Kibana từ
[đây](http://www.elasticsearch.org/overview/elkdownloads/)

### 1. ElasticSearch

Giải nén, vào thư mục vừa giải nén chạy lệnh ./bin/elasticsearch để khởi động.
<br/>
Cấu hình cho ElasticSearch tại: `./config/elasticsearch.yml`.

### 2. Logstash
Giải nén, vào thư mục vừa giải nén chạy ./bin/logstash -f *path_to_config_file*
<br/>
với *path_to_config_file* là đường dẫn đến file cấu hình của **logstash** kết thúc bằng đuôi `.conf`.
### 3. Cài đặt Kibana
Tải về **Kibana** từ [đây](http://www.elasticsearch.org/overview/elkdownloads/)
<br/>
Để có thể chạy được trên trình duyệt, **Kibana** cần được chạy trên một server, ví dụ **Apache**, **Nginx**,...

##### 3.1. Nginx server

```
sudo mkdir -p /srv/www/kibana
sudo tar xf kibana-3.1.0.tar.gz -C /srv/www/  
sudo chown -R www-data:www-data /srv/www/  
```
Ở đây tôi dùng phiên bản `3.1.0`, bạn sẽ thay `3.1.0` bằng tên phiên bản Kibana mà bạn dùng.

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
##### 3.2. Trên **apache** server
Chỉ cần giải nén vào thư mục `/var/www/kibana`
```
sudo mkdir -p /var/www/kibana
sudo tar xf kibana-3.1.0.tar.gz -C /var/www/kibana
```
