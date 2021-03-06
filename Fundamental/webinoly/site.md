## Command **site**
* Dùng để tạo bất kỳ một trang web mới của bạn, thêm chứng chỉ SSL, kích hoạt FastCgi Cache cho cài đặt WordPress của bạn và nhiều tính năng khác sẽ cho phép bạn có toàn quyền kiểm soát các trang web của mình một cách dễ dàng và đáng tin cậy, luôn luôn ở một lệnh 

`sudo site <domain> <option> <option2>`

Option:
* -cache : bật tắt chế độ cache
* -clone-from : sao chép từ một trang web wp khác
* -delete : xóa bỏ
* -delete-all: Xóa hết
* -force-redirect=(Option)
    * www:
    * root
    * off
* -forward: chuyến tiếp chuyển hướng trang web
* –html: Tạo trang web HTML
* –php : tạo trang PHP  
* -wp : Tạo một website Wordpress 
* –mysql: Tạo một trang web php kết hợp với cơ sở dữ liệu
* -list: Hiển thị các trang web đã tạo
* -multisite-convert
* -parked: thay thế bổ sung trỏ đến trang web chính
* -proxy: 
* -redirection: định hướng
* –ssl: Chứng chỉ bảo mật 
### Tạo một trang web mới
Tạo một trang web mới thuộc bất kỳ loại nào: HTML, PHP, WordPress, Reverse Proxy, v.v.

Tạo một trang HTML cơ bản:

`site demo.com -html`

Ví dụ:
```
~# site html.dh.vn -html

~# echo "Website's HTML" > www/html.dh.vn/htdocs/index.html
```

![Imgur](https://i.imgur.com/ZRugttj.png)

Tạo một trang web có hỗ trợ PHP

`site demo.com -php`

Demo

```
~# site php.dh.vn -php
echo "<?php phpinfo(); ?>" > www/php.dh.vn/htdocs/index.php
```

![Imgur](https://i.imgur.com/LzZLfut.png)

Tạo một trang web PHP kết hợp với cơ sở dữ liệu:

`site demo.com -mysql`

Demo
```
~# site sql.dh.vn -mysql

Site sql.dh.vn has been successfully created!


Database Host: localhost
Database Name: sql_dh_vn
Database User: sql_dh_vn
Password: 6nSETl0yqURN93P5

Database successfully created!
```
Tùy chọn chỉnh sửa web trước khi tạo:

`site web.duonghuy.xyz  -mysql=custom`

Câu lệnh trên dùng để tạo một trang web và người tạo phải truyền vào dữ liệu của MySQL

### Tạo 1 trang WordPress
```
site domain -wp
#lệnh sau để tắt login admin khi truy cập
httpauth domain -wp-admin=off
```

![Imgur](https://i.imgur.com/g3tVIzb.png)

![Imgur](https://i.imgur.com/xvLGf4i.png)

![Imgur](https://i.imgur.com/QMfLd4B.png)

![Imgur](https://i.imgur.com/9DpR3Of.png)

![Imgur](https://i.imgur.com/oAdCcrk.png)
 

### WordPress Multisite
Chuyển đổi trang WordPress sang [WordPress Multisite](Note/multisite.md)

`site example.com -multisite-convert`

Có 2 lựa chọn để quản lý là **Subdomain** và **Subfolder**

### Bộ nhớ cache FastCGI

Cùng với Nginx tối ưu hóa để tăng tốc trang web WordPress của mình. 

Để bật FastCGI:

`sudo site example.com -cache=on`

Để tắt FastCGI:

`sudo site example.com -cache=off`

Cũng có thể kích hoạt nó từ việc tạo trang mới

`sudo site example.com -wp -cache=on`

### Cấu hình thư mục con
Khả năng định cấu hình độc lập từng thư mục con của một trang web cụ thể.

```
sudo site example.com -html -subfolder=/one
sudo site example.com -php -subfolder=/two
sudo site example.com -wp -subfolder=/three
sudo site example.com -proxy -subfolder=/four

# Delete a subfolder site
sudo site example.com -delete -subfolder=/xxx
```

Là một tùy chon để có một trang web ở gốc của tên miền.
Nó có thể hỗ trợ các thư mục con được cấu hình trong bất kỳ kết hợp cấu hình nào bạn cần

```
# Example with subfolders and empty root
+ example.com <empty>
  - /blog <wp>
  - /downloads <proxy>
  - /tickets <php>

# Another example with static site in root
+ example.com <html>
  - /news <wp>
  - /clients <php>
```
### Tên miền chưa được sử dụng hoặc bí danh
Một tên miền chưa được sử dụng là một tên miền bổ sung hoặc thay thế trỏ đến trang web chính. Đó là một cách đơn giản để truy cập trang web của bạn từ các tên miền khác nhau.

`sudo site example.com -parked`

```
~# site web1.dh.vn -parked

Site web1.dh.vn has been successfully created!
#Nhập tên miền muốn chuyển tiếp đến
Main site domain: wp4.dh.vn

Parked domain was successfully configured!
```
Khi thực hiện lệnh, bạn sẽ được yêu cầu nhập tên miền chính nơi tên miền chưa được sử dụng mới sẽ trỏ đến. Theo cùng một cách bạn có thể sử dụng lệnh sau để tạo điều kiện cho việc sử dụng nó:

`sudo site example.com -parked=mainsite.com`

Đảm bảo trang web chính được lưu trữ trên cùng một máy chủ.

### Chuyển tiếp tên miền 

Tất cả các yêu cầu cho tên miền này sẽ được chuyển hướng hoặc chuyển tiếp đến tên miền khác.

`sudo site example.com -forward`

```
~# site wp.dh.com -forward

#Nhập tên miền muốn chuyển tiếp
Destination domain: wp.dh.vn
Site wp.dh.com has been successfully created!
Every request to wp.dh.com will be redirected to http://wp.dh.vn
```
Tất cả các tham số yêu cầu được chuyển đến tên miền mới. Nếu bạn muốn xóa các tham số này và buộc chuyển hướng đến một trang web duy nhất, Bạn có thể sử dụng tùy chọn `-root=on`


### Reverse Proxy site

Để tạo một trang web có cấu hình Reverse Proxy trong Nginx:

`sudo site example.com -proxy=[localhost:8080]`



### Tạm thời vô hiệu hóa một trang web

Bất cứ lúc nào cũng có thể kích hoạt hoặc tạm ngưng một trang web được lưu trữ trên máy chủ của bạn mà không xóa nó

`sudo site example.com -on`

hoặc

`sudo site example.com -off`

#### Xóa một trang web
Sử dụng câu lệnh một cách cẩn thận, vì khi đã xóa một trang web, nó sẽ không thể khôi phục các tệp dữ liệu

`sudo site example.com -delete`

Để xóa tất cả các trang web trên máy chủ của bạn:

`site -delete-all`

Khi một trang web bị xóa, nếu tìm thấy CSDL bên ngoài, một nỗ lực sẽ được thực hiện để sử dụng dữ liệu `-external-db`, nếu chúng không được tìm thấy, người dùng sẽ được yêu cầu nhập dữ liệu cần thiết. `-revoke=on` Tham số này sẽ xóa và thu hồi Chứng chỉ SSL của một trang web nếu được tìm thấy, hãy sử dụng tùy chọn `off` để giữ chứng chỉ SSL.

#### Danh sách các trang web
Để xem danh sách tất cả các trang web của bạn được lưu trữ trên máy chủ, hãy sử dụng lệnh sau:

`site -list`


### Chứng Chỉ SSL với Let's-Encrypt 

`site domail.com --ssl=on`

* Trong quá trình tạo chứng chỉ, Webinoly sẽ gửi yêu cầu tài khoản Email của bạn, Email này sẽ được sử dụng để đăng ký chứng chỉ mới, ngoài ra còn giúp bạn theo dõi quá trình gia hạn định kỳ.

* Các chứng chỉ do Let Encrypt cấp có giá trị trong khoảng thời gian 90 ngày. Webinoly tự động kiểm tra mỗi tuần một lần trạng thái của các chứng chỉ. Webinoly tự động hoàn tất quy trình để giữ cho chứng chỉ của bạn và các trang web của bạn luôn có hiệu lực.


* Vô hiệu hóa SSl trên một trang web
Bận hủy kích hoạt việc sử dụng SSL trong trang web, thực hiện lệnh sau:

`site example.com -ssl=off`

Các tùy chọn `ssl=off` có thể được sử dụng nhay cả khi tran gweb của bạn thậm chí không tồn tại nữa như là một cách để loại bỏ và thu hồi SSL.
### Chứng chỉ trên các trang web packed
Tùy chọn `-root` cho phép bạn tạo chứng chỉ cho các trang web nơi gốc của các tệp của bạn ở một nơi khác so với thông thường
### Chứng chỉ trên các trang web Reverse Proxy
Tùy chọn `-root-path` cho phép chúng tôi chỉ định một tuyến đường đi khác, như trường hợp của các trang web trong cấu hình Reverse Proxy nơi các tệp được lưu trữ ở một vị trí khác với ***/var/www***

`site example.com -ssl=on -root-path=/path`

### Bắt buộc WWW hoặc không trong một trang Web

Theo mặc định, Webinoly cấu hình trang web của bạn để chấp nhận cả hai yêu cầu trong tên miền của ban, nghĩa là demo.com và www.demo.com sẽ hợp lệ. Bạn có thể buộc sử dụng và chuyển hướng các yêu cầu đến bất kỳ tùy chọn nào.

`site example.com -force-redirect=<options>`

Option:

* www
* root
* off

### Clone 1 website WordPress

Bạn có thể sao chép bất kỳ trang web WordPress nào.

Tính năng này còn được gọi là các trang dàn dựng của web trong giai đoạn phát triển.

`site example.com -clone-from=dev.example.com`

### Thay thế nội dung

Có thể được sử dụng một mình trong bất kỳ trang web WordPress nào khác để tìm kiếm thay thế bất kỳ từ hoặc chuỗi nào trong nội dung của bạn.

`site example.com -replace-content`

Để sao chép hoặc thay thế nội dung -được kết nối với cơ sở dữ liệu bên ngoài, cần phải nhập tên người dùng và mật khẩu. Để bỏ qua những câu hỏi này, bạn có thể sử dụng tùy chọn `-external-db=[user,pass]`
