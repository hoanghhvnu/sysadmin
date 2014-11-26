### Rewrite url for Kibana
Để quản lý nhiều dashboard cho Kibana, ta có thể lưu thông tin dashboard vào trong ES. Bằng nút **Save** ở góc phải trên. Chưa rõ vì lý do gì mà tôi chưa lưu được theo cách này, có lẽ vì kibana đang dùng trên một máy client, muốn lưu thông tin này vào ES của server thì cần mở cổng kết nối tới.

Còn một cách khác, đó là **export** dashboard ra file JSON, sau đó copy đến thư mục `app/dashboard` của thư mục chứa *kibana* trên *Logstash Server*.
Sau đó truy cập bằng trực tiếp đường dẫn:
```
localhost/kibana/index.html#/dashboard/file/logstash.json
```

Trong đó `logstash.json` chính là file cấu hình cho **dashboard**. Chúng ta sẽ **Rewrite url** để đơn giản hơn trong việc truy cập và quản lý cho nhiều người.

#### NGINX
Trong **nginx**, các luật *rewrite* sẽ được cấu định nghĩa trong file cấu hình ở thư mực `site-availables` (với Debian base), hoặc `conf.d`. Luật rewrite có thể viết trong *server*, *location* scope.

**Lưu ý:** nếu việc rewrite không hoạt động như mong muốn: gặp lỗi 404 hoặc 500,... Bạn cần bật chế độ *log* cho rewrite.
Bằng cách thêm vào trong file config: `rewrite_log on;`
Tuy nhiên, việc rewrite sẽ được ghi vào *logfile* ở mức `notice`, sẽ không thấy trong `error.log` của nginx. Chúng ta phải chỉ định file log error cùng với *flag* chỉ định sẽ ghi cả log mức `notice`:
`error_log /var/log/nginx/error_custom.log notice;`

Khi hết một dòng luật rewrite, thường sẽ kết thúc bằng `last` hoặc `break`. Nhưng trong trường hợp của chúng ta thì kibana không chỉ chạy link đó mà còn request thêm nhiều nữa nên chúng ta phải dùng `permanent` thay vì hai từ khóa ở trên.
