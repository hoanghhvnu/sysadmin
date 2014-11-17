# Cài đặt Oracle Java JDK cho linux
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

###Nguồn tham khảo
**Wikilinux** [link](http://wikilinux.vn/cai-dat-oracle-jdk-7-cho-linux/)
