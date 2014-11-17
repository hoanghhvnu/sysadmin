# ELK
## Logstash tự động gửi email cho người quản trị
Khi một bản ghi trong log thỏa mãn điều kiện đặt trước, Logstash sẽ tự động gửi email cho người quản trị.

Cú pháp:
```
output {
  email {
    attachments => ... # array (optional), default: []
  	body        => ... # string (optional), default: ""
  	cc          => ... # string (optional)
  	codec       => ... # codec (optional), default: "plain"
  	contenttype => ... # string (optional), default: "text/html; charset=UTF-8"
  	from        => ... # string (optional), default: "logstash.alert@nowhere.com"
  	htmlbody    => ... # string (optional), default: ""
  	options     => ... # hash (optional), default: {}
  	replyto     => ... # string (optional)
  	subject     => ... # string (optional), default: ""
  	to          => ... # string (required)
  	via         => ... # string (optional), default: "smtp"
  	workers     => ... # number (optional), default: 1
  }
}
```
**To:** Trường bắt buộc, là địa chỉ email nhận thông báo.
<br/>
**Subject, body, cc,...** là những trường tùy chọn, để cấu hình chi tiết hơn cho việc gửi email.
**via:** là trường cần chú ý: Ở đây ta sẽ dùng phương thức __*sendmail*__ `via => "sendmail"`.

***Chú ý:** Email sẽ không được gửi ngay khi file log vừa được ghi mà sẽ sau một vài phút.

###Cấu hình bộ lọc cho việc gửi email
Không phải bản ghi log nào cũng cần thiết phải gửi email cho người quản trị, chỉ nên gửi đối với những bản ghi mang thông tin quan trọng.<br/>
Ví dụ trong log java ta có các *loglevel* là: `ERROR`, `WARN`, `INFO`... Khi chạy chương trình thì chỉ loglevel `ERROR` là đáng quan tâm và nghiêm trọng nhất. Vậy ta sẽ cấu hình để cứ khi có log `ERROR` thì Logstash sẽ tự động gửi email cho người quản trị.

Giả sử thông tin về *loglevel* được được thể hiện ở trường `level`. Khi đó ta sẽ cấu hình như sau:
```
output{
  if [level] == "ERROR" {
    email{
		from      => "hoanghh@monkey"
		to      => "hoanghh@gmail.com"
		subject => "Logstash_Javalog_Error"
		body    => "%{message}"
		via     => "sendmail"
    }
  }
}
```
Chú ý rằng nội dung email sẽ là nội dung của trường `message`, tức cả bản ghi.
