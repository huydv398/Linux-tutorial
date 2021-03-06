# Lab: SSH Keypairs Ubuntu
1. [Tạo Key RSA trên Client](#1)
2. [Copy Public key vào SSH Server ](#2)
3. [Đăng nhập xác thực](#3)
4. [Tắt xác thực trên máy Server](#4)
## Bước 1: Tạo cặp Key RSA trên **SSH** Client:
<a name="1"></a>

* Bước đầu tạo ra cặp **SSH** Key Pair Trên **SSH** Client hay chính máy tính thực hiện **SSH**:
* Mặc định, lệnh `ssh-keygen` sẽ tạo ra 1 cặp **RSA** key pair *2048-bit*, gần như đáp ứng đủ mọi trường hợp. Nếu muốn cặp key phức tạp hơn, có thể tạo key với độ dài (4096-bit) bằng option `-b 4096`.

![Imgur](https://i.imgur.com/J0jKKq4.png)

* Sau khi thực hiện, bạn sẽ nhìn thấy output sau:
![Imgur](https://i.imgur.com/cKaPCGS.png)
* Gõ `Enter` để lưu cặp key vào thư mục con `.ssh/` nằm trong thư mục `home` của user hiện hành, hoặc tự chọn 1 con đường khác.
* Nếu trên máy đã có 1 cặp key từ trước đó, bạn sẽ nhìn thấy output sau:<br>
    * `y`: để ghi đè
    * `n`: không ghi đè

* Nếu chọn "`overwrie the key on disk `", bạn sẽ không thể xác thực các key đang sử dụng trước đây nữa.
* Sau khi lựa chọn, sẽ thấy output tiếp theo :
* Đây là tùy chọn thêm 1 chuỗi mật khẩu, được khuyến nghị để tăng tính bảo mật. nếu nhập chuỗi `passpharase` này , bạn sẽ phải gõ thêm chúng bất kỳ lúc nào sử dụng key( chỉ trừ khi sử dngj phần mềm để SSH đã lưu trữ passphase). Nếu không muốn sử dụng passphrase, sẽ thấy output sau :
## Bước 2 - Copy Public key vào SSH server:
<a name="2"></a>

* Cách nhanh nhất để copy Public key trên CentOS là sử dụng tiện ích `ssh-copy-id` vì nó khá đơn giản. Nếu không có sẵn `ssh-copy-id`, cần phải copy 1 cách thủ công.
### Cách 1 - Copy Public Key sử dụng `ssh-copy-id`
* Công cụ `ssh-copy-id` thường có săn trên hệ điều hành. Nếu dùng cách này, cần có kết nối SSH bằng mật khẩu từ client đến Server :
* Điều này có nghĩa máy tính hiện tại đang không nhận ra máy chủ đầu xa. Hiện tượng này sẽ xảy ra trong lần đầu kết nối tới 1 host mới gõ `yes` hoặc ENTER để tiếp tục.
![Imgur](https://i.imgur.com/cKaPCGS.png)
* Tiếp theo, Công cụ sẽ dò quét 1 file `id_rsa.pub` trên máy vừa tạo ra. Nếu tìm thấy, nó sẽ hỏi mật khẩu của User SSH:

* Nhập mật khẩu và gõ ENTER. Công cụ sẽ kết nối tới server bằng tài khoản được cung cấp. Sau đó nó sẽ copy nội dung. Sau đó nó sẽ copy nội dung file `~/.ssh.id_rsa.pub` vào 1 file tên là `authorized_keys` trong thư mục `~/.ssh` của Server 
![Imgur](https://i.imgur.com/GmT0FG7.png)
* Tại bước này, key `id_rsa.pub` đã được upload lên Server.
![Imgur](https://i.imgur.com/4VESCIL.png)
### Cách 2 Copy Public key sử dụng SSH
* Nếu không có sẵn tiện ích  `ssh-copy-id`, có thể sử dụng phương pháp truyền thống để copy public key sang server 
* Tại máy server :
    * Tạo 1 thư mục `mkdir -p ~/.ssh`
    * Tạo 1 file **authorized_keys** `touch ~/.ssh/authorized_keys`
    * Copy file `id_rsa.pub` từ máy client vào file `authorized_keys` trên máy 
* Hoặc sử dụng lệnh sau:
`cat ~/.ssh/id_rsa.pub | ssh root@192.168.5.20 "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"`

## Bước 3 Xác thực trên CentOS Server sử dụng SSH Key
<a name="3"></a>

* Sau khi hoàn thành các bước trên, nhập lệnh sau để **SSH** vào server:
    * **command**: `# ssh [user]@ip_server`
    ![Imgur](https://i.imgur.com/mpkin58.png)
* Điều này có nghĩa máy tính hiện tại đang không nhận ra máy chủ đầu xa. Gõ `yes` hoặc `Enter` để tiếp tục.

* Nếu đã tạo passphrase thì ở bước này phải nhập thêm passphrase, nếu không có thì có thể truy cập được luôn.
## Bước 4 Disable ssh pasword và user trên Server 
<a name="4"></a>
* Mặc định, tồn tại song song cả 2 chế độ xác thực qua **SSH key** và xác tực qua mật khẩu. Vì vậy, vẫn có kahr năng Server bị tấn công bằng **Brute Force**.
* Trên server, mở file cấu hình `sshd`:

`# sudo vi /etc/ssh/sshd_config`

* Sử dụng `:set nu` di chuyển đến dòng `58-34`như sau:
```
PasswordAuthentication no
PermitRootLogin no
```
* Lưu lại Restart dịch vụ **SSH**:
`# sudo systemctl restart sshd.service`

Sau đó thực hiện kết nối **SSH**. Server sẽ hoàn toàn xác thực bằng **SSH** Key đã add trước đó mà không thể xác thực qua mật khẩu nữa .

