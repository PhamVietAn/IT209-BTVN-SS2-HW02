# Bài 2: Khởi tạo người dùng thường và thiết lập đặc quyền quản trị

## Bước 1: Đăng nhập vào Droplet bằng tài khoản root

**Câu lệnh trên máy cá nhân:**

```powershell
ssh -i C:\Users\crou\.ssh\id_ed25519 root@167.99.72.156
```

## Bước 2: Cập nhật danh sách gói phần mềm

**Câu lệnh trên Droplet:**

```bash
apt update
```

## Bước 3: Tạo tài khoản devops

**Câu lệnh trên Droplet:**

```bash
adduser devops
```

Nhập mật khẩu mạnh cho tài khoản `devops`. Các trường thông tin như họ tên, số phòng và số điện thoại có thể bỏ trống bằng cách nhấn `Enter`. Cuối cùng nhập `Y` để xác nhận.

**Kết quả hiển thị:**

```text
Adding user `devops' ...
Adding new group `devops' (1001) ...
Adding new user `devops' (1001) with group `devops' ...
Creating home directory `/home/devops' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Is the information correct? [Y/n] Y
```

## Bước 4: Thêm tài khoản devops vào nhóm sudo

**Câu lệnh thêm người dùng vào nhóm sudo:**

```bash
usermod -aG sudo devops
```

**Câu lệnh kiểm tra nhóm:**

```bash
groups devops
```

**Kết quả hiển thị:**

```text
devops : devops sudo
```

Tham số `-aG` có ý nghĩa:

- `-a`: bổ sung nhóm mới mà không xóa các nhóm hiện có của người dùng.
- `-G`: chỉ định danh sách nhóm bổ sung.

## Bước 5: Sao chép cấu hình SSH từ root sang devops

Khi đang đăng nhập bằng `root`, thực hiện lệnh sau.

**Câu lệnh sao chép SSH Key:**

```bash
rsync --archive --chown=devops:devops /root/.ssh /home/devops/
```

Lệnh trên sao chép thư mục `.ssh` cùng tệp `authorized_keys`, đồng thời đổi chủ sở hữu thành `devops:devops`.

Nếu không sử dụng `rsync`, có thể thực hiện thủ công.

**Các câu lệnh thay thế:**

```bash
mkdir -p /home/devops/.ssh
cp /root/.ssh/authorized_keys /home/devops/.ssh/authorized_keys
chown -R devops:devops /home/devops/.ssh
chmod 700 /home/devops/.ssh
chmod 600 /home/devops/.ssh/authorized_keys
```

## Bước 6: Kiểm tra quyền truy cập

Thiết lập lại quyền để bảo đảm SSH chấp nhận tệp khóa.

**Các câu lệnh phân quyền:**

```bash
chown -R devops:devops /home/devops/.ssh
chmod 700 /home/devops/.ssh
chmod 600 /home/devops/.ssh/authorized_keys
```

**Các câu lệnh kiểm tra:**

```bash
ls -ld /home/devops/.ssh
ls -l /home/devops/.ssh/authorized_keys
```

**Kết quả hiển thị:**

```text
drwx------ 2 devops devops 4096 Oct  2 11:53 /home/devops/.ssh
-rw------- 1 devops devops  104 Oct  2 11:53 /home/devops/.ssh/authorized_keys
```

## Bước 7: Kiểm tra đăng nhập bằng tài khoản devops

Giữ nguyên phiên SSH của `root` để có thể khắc phục nếu cấu hình chưa đúng. Mở một cửa sổ Terminal mới trên máy cá nhân.

**Câu lệnh đăng nhập:**

```powershell
ssh -i C:\Users\crou\.ssh\id_ed25519 devops@167.99.72.156
```

Sau khi đăng nhập, kiểm tra tên người dùng.

**Câu lệnh kiểm tra:**

```bash
whoami
```

**Kết quả hiển thị:**

```text
devops
```

## Bước 8: Kiểm tra quyền sudo

**Câu lệnh kiểm tra quyền sudo:**

```bash
sudo whoami
```

Nhập mật khẩu đã đặt cho tài khoản `devops`.

**Kết quả hiển thị:**

```text
root
```

## Log Terminal kiểm tra

**Log Terminal ghi nhận đăng nhập và quyền quản trị:**

```text
PS C:\Users\crou> ssh -i C:\Users\crou\.ssh\id_ed25519 devops@167.99.72.156
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-157-generic x86_64)

devops@ptit-devops-ss02:~$ whoami
devops
devops@ptit-devops-ss02:~$ groups
devops sudo
devops@ptit-devops-ss02:~$ sudo whoami
[sudo] password for devops:
root
devops@ptit-devops-ss02:~$
```

## Kết luận

Tài khoản `devops` đã được tạo, thêm vào nhóm `sudo` và cấu hình đăng nhập bằng SSH Key. Quyền của thư mục `.ssh` và tệp `authorized_keys` được thiết lập đúng, cho phép tài khoản đăng nhập và thực thi lệnh quản trị thông qua `sudo`.
