# Hướng Dẫn Thiết Lập Dịch Vụ quai-stratum

## Giới thiệu
Hướng dẫn này sẽ chỉ bạn cách tạo và chạy một script `stratum.sh` dưới dạng một dịch vụ `systemd` với tên `quai-stratum.service` trên hệ điều hành Linux.

## Bước 1: Tạo script `stratum.sh`
Trước tiên, bạn cần tạo một file script có tên `stratum.sh`. File này sẽ chứa các lệnh bạn muốn chạy dưới dạng dịch vụ.

### Tạo file `stratum.sh` trong thư mục home của bạn (ví dụ `/home/username/stratum.sh`).

```bash
nano ~/stratum.sh
```
### Thêm nội dung sau vào file stratum.sh:
```bash
#!/bin/bash
cd $HOME/go-quai-stratum
./go-quai-stratum --region=cyprus --zone=cyprus1 --stratum=3334
```
### Lưu file và thoát trình soạn thảo (Ctrl + X, sau đó nhấn Y và Enter).
### Đảm bảo script stratum.sh có quyền thực thi:
```bash
chmod +x ~/stratum.sh
```
## Bước 2: Tạo file dịch vụ quai-stratum.service
### Tiếp theo, tạo một file dịch vụ systemd để quản lý và chạy script này.
### Tạo file dịch vụ quai-stratum.service trong thư mục /etc/systemd/system/.
```bash
sudo nano /etc/systemd/system/quai-stratum.service
```
### Thêm nội dung sau vào file quai-stratum.service:
```bash
[Unit]
Description=Quai Stratum Service
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash /home/username/stratum.sh
Restart=on-failure
User=username

[Install]
WantedBy=multi-user.target
```
### Lưu ý: Thay thế username bằng tên người dùng thực tế của bạn.

### Lưu file và thoát trình soạn thảo (Ctrl + X, sau đó nhấn Y và Enter).
## Bước 3: Khởi động và kiểm tra dịch vụ
### Tải lại daemon của systemd để nhận biết dịch vụ mới:
```bash
sudo systemctl daemon-reload
```
### Khởi động dịch vụ quai-stratum:
```bash
sudo systemctl start quai-stratum.service
```
### Đảm bảo dịch vụ sẽ tự động khởi động lại khi hệ thống khởi động:
```bash
sudo systemctl enable quai-stratum.service
```
### Kiểm tra trạng thái của dịch vụ:
```bash
sudo systemctl status quai-stratum.service
```
### Bạn sẽ thấy thông tin về trạng thái của dịch vụ, nếu dịch vụ chạy thành công, bạn sẽ thấy trạng thái active (running).

## Bước 4: Khắc phục sự cố
### Nếu dịch vụ không chạy thành công, hãy kiểm tra nhật ký để tìm hiểu nguyên nhân:
```bash
sudo journalctl -u quai-stratum.service
```
### Kiểm tra các thông báo lỗi và điều chỉnh file stratum.sh hoặc file dịch vụ quai-stratum.service nếu cần thiết.

## Kết luận
### Với các bước trên, bạn đã thiết lập thành công dịch vụ quai-stratum trên hệ thống của mình. Nếu gặp bất kỳ vấn đề gì, hãy kiểm tra lại các bước và đảm bảo rằng tất cả các cấu hình đều chính xác.
```bash
Hãy đảm bảo thay thế `username` bằng tên người dùng thực tế của bạn trong cả file `stratum.sh` và `quai-stratum.service`.
```
