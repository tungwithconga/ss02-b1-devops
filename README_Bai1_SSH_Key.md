# Bài 1: Khởi tạo máy chủ Ubuntu và cấu hình SSH Key

## 1. Mục tiêu

-   Khởi tạo máy chủ chạy Ubuntu 22.04 LTS.
-   Kết nối từ máy cá nhân tới máy chủ bằng SSH.
-   Tạo cặp khóa SSH bằng thuật toán ED25519.
-   Cấu hình Public Key trên máy chủ.
-   Đăng nhập bằng Private Key thay cho mật khẩu.

> **Lưu ý:** Phần thực hành kỹ thuật này sử dụng VPS Ubuntu 22.04 hiện
> có thay cho DigitalOcean Droplet. Quy trình SSH Key tương đương về mặt
> kỹ thuật.

## 2. Thông tin môi trường thực hành

-   Hệ điều hành Server: Ubuntu 22.04 LTS
-   Máy cá nhân: Windows
-   Công cụ kết nối: OpenSSH / Windows Terminal
-   User thực hành: `tung`
-   Phương thức xác thực ban đầu: Password
-   Phương thức xác thực sau cấu hình: SSH Key (ED25519)

> Không đưa mật khẩu, Private Key hoặc thông tin bí mật lên GitHub.

## 3. Kết nối SSH tới Server

Từ CMD/PowerShell trên Windows:

``` bash
ssh tung@<SERVER_IP>
```

Nhập mật khẩu của user `tung` để đăng nhập.

Sau khi đăng nhập thành công, Terminal hiển thị giao diện Ubuntu và dấu
nhắc dạng:

``` text
tung@<hostname>:~$
```

## 4. Tạo SSH Key trên máy cá nhân

Thoát khỏi Server:

``` bash
exit
```

Tại CMD/PowerShell trên Windows, tạo cặp khóa ED25519:

``` bash
ssh-keygen -t ed25519 -C "tung"
```

Sử dụng đường dẫn mặc định khi được hỏi nơi lưu key.

Hai file được tạo:

``` text
~/.ssh/id_ed25519       # Private Key
~/.ssh/id_ed25519.pub   # Public Key
```

Trong đó:

-   `id_ed25519`: khóa bí mật, chỉ lưu trên máy cá nhân và không chia
    sẻ.
-   `id_ed25519.pub`: khóa công khai, được phép đưa lên Server.

## 5. Lấy Public Key

Trên Windows CMD:

``` bash
type %USERPROFILE%\.ssh\id_ed25519.pub
```

Public Key có dạng:

``` text
ssh-ed25519 AAAA... tung
```

Copy toàn bộ Public Key.

## 6. Thêm Public Key vào Server

Đăng nhập lại Server:

``` bash
ssh tung@<SERVER_IP>
```

Tạo thư mục `.ssh` và thiết lập quyền:

``` bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

Mở file chứa các Public Key được phép đăng nhập:

``` bash
nano ~/.ssh/authorized_keys
```

Paste Public Key vào file, sau đó lưu bằng:

``` text
Ctrl + O
Enter
Ctrl + X
```

Thiết lập quyền cho file:

``` bash
chmod 600 ~/.ssh/authorized_keys
```

## 7. Kiểm tra đăng nhập bằng SSH Key

Từ CMD/PowerShell trên máy cá nhân:

``` bash
ssh -i %USERPROFILE%\.ssh\id_ed25519 tung@<SERVER_IP>
```

### Kết quả mong đợi

SSH đăng nhập thành công vào Ubuntu Server mà không yêu cầu mật khẩu của
user `tung`.

Terminal hiển thị:

``` text
Welcome to Ubuntu 22.04.x LTS
...
tung@<hostname>:~$
```

## 8. Giải thích cơ chế

SSH Key sử dụng một cặp khóa:

``` text
Máy cá nhân                         Server
────────────                        ────────────
id_ed25519                          authorized_keys
Private Key                         Public Key
     │                                  │
     └──────── SSH Authentication ──────┘
```

Private Key luôn được giữ trên máy cá nhân. Public Key được lưu trên
Server. Khi kết nối, SSH sử dụng cơ chế mật mã để chứng minh máy khách
sở hữu Private Key tương ứng mà không cần gửi Private Key lên Server.

## 9. Kết quả thực hành

-   [x] Máy chủ chạy Ubuntu 22.04 LTS.
-   [x] Kết nối SSH bằng user riêng thành công.
-   [x] Tạo cặp khóa ED25519 thành công.
-   [x] Thêm Public Key vào `~/.ssh/authorized_keys`.
-   [x] Thiết lập permission cho `.ssh` và `authorized_keys`.
-   [x] Kết nối Server bằng SSH Key thành công.

## 10. Minh chứng

Chèn ảnh chụp vào thư mục bài tập, ví dụ:

``` text
homework/session_02/ex1/
├── README.md
└── images/
    ├── ssh-keygen.png
    └── ssh-login-success.png
```

Sau đó thêm ảnh vào README:

``` markdown
### Tạo SSH Key

![Tạo SSH Key](images/ssh-keygen.png)

### Kết nối SSH thành công

![SSH Login](images/ssh-login-success.png)
```

## 11. Cấu trúc nộp bài

``` text
homework/
└── session_02/
    └── ex1/
        ├── README.md
        └── images/
```

## 12. Kết luận

Qua bài thực hành, đã cấu hình thành công xác thực SSH bằng cặp khóa
ED25519 trên Ubuntu 22.04. Việc sử dụng SSH Key giúp hạn chế việc sử
dụng mật khẩu trực tiếp khi đăng nhập máy chủ và là phương thức xác thực
phổ biến trong quản trị hệ thống và DevOps.
