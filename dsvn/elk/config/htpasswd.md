## Quản lý quyền truy cập
Để bảo mật một thư mục nào đó trên **nginx**, ta có thể sử dụng `htpasswd`.
Đầu tiên là kiểm tra xem trên hệ thống đã cài đặt `htpasswd` chưa, bằng cách chạy lệnh:
`which htpasswd`.

Nếu chưa cài thực hiện việc cài đặt trước tiên.

Trên **Red hat base:**
```
sudo yum install httpd-tools
```
Trên **Debian base:**
```
sudo apt-get install apache2-utils
```

Để sinh file .htpasswd chạy lệnh:
```
sudo htpasswd -c /etc/nginx/.htpasswd exampleuser
```
Trong đó `/etc/nginx/.htpasswd` có thể thay đổi bằng đường dẫn và tên file khác. `exampleuser` là tên đăng nhập mong muốn.
Sau khi gõ `Enter` thì sẽ hỏi mật khẩu, nhập hai lần là xong.

Tiếp theo cần cấu hình trong file config (thuộc thư mục conf.d, hoặc sites-availables trong Debian base). Trong block `server`, hoặc `location`:
```
auth_basic "Restricted";
auth_basic_user_file /etc/nginx/.htpasswd;
```
Trong đó, nội dung của trường `auth_basic` sẽ là thông báo trong cửa sổ hỏi password. `auth_basic_user_file` là đường  dẫn đến file `.htpasswd` cho thư mục đó.

Lưu lại và khởi động lại nginx:
```
sudo /etc/init.d/nginx restart
```
