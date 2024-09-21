# Hướng Dẫn Cài Đặt Node Quai Network

## Giới Thiệu
Hướng dẫn này sẽ giúp bạn cài đặt go-quai, phiên bản Go của Quai Network. Bài hướng dẫn này chủ yếu dành cho các hệ thống Linux và MacOS. Hiện tại, việc chạy go-quai trên Windows hoặc WSL2 không được hỗ trợ.

## Yêu Cầu
Quai Network bao gồm nhiều "slice" hoặc shard thực thi, hoạt động cùng nhau để hình thành một mạng lưới lớn hơn. Một node toàn cầu chạy tất cả các slice, trong khi một slice node chỉ chạy một slice duy nhất. Chúng tôi khuyến nghị bạn chạy một slice node.

### Để chạy một global node trong mạng thử nghiệm Iron Age, bạn cần một máy MacOS hoặc Ubuntu với các thông số kỹ thuật sau:
- **CPU:** Nhanh với 16+ lõi
- **RAM:** 64GB+
- **SSD:** Nhanh với ít nhất 3TB dung lượng trống
- **Kết nối Internet:** Tốc độ tải xuống 10+ MBit/sec

### Để chạy một slice node trong mạng thử nghiệm Iron Age, bạn cần một máy MacOS hoặc Ubuntu với các thông số kỹ thuật sau:
- **CPU:** Nhanh với 8+ lõi
- **RAM:** 24GB+
- **SSD:** Nhanh với ít nhất 1TB dung lượng trống
- **Kết nối Internet:** Tốc độ tải xuống 10+ MBit/sec

## Cài Đặt Các Phụ Thuộc
Để chạy go-quai, bạn cần cài đặt một số phụ thuộc. Bạn có thể sử dụng trình quản lý gói yêu thích của mình (apt, brew, v.v.). Các phụ thuộc cần thiết bao gồm:

Go v1.22.0+
Cài Đặt Snap Trên Linux
Snap không được cài đặt mặc định trên tất cả các bản phân phối Linux.
```bash
# cài đặt snapd nếu bạn chưa có
sudo apt install snapd

# cài đặt go
sudo snap install go --classic
```
Đối với các bản phân phối khác hoặc macOS, tham khảo trang cài đặt Golang: https://golang.org/doc/install

Git, Make, và G++
Cài đặt git, make, và g++ với lệnh sau:
```bash
# cài đặt git, make, và g++
sudo apt install git make g++
```
Clone go-quai
Bây giờ các phụ thuộc đã được cài đặt, chúng ta có thể clone repository go-quai.

```bash
git clone https://github.com/dominant-strategies/go-quai
cd go-quai
```
Kiểm tra phiên bản mới nhất: https://github.com/dominant-strategies/go-quai/tags
Ví dụ v0.32.0

```bash
git checkout v0.32.0
```
Build go-quai
Chạy lệnh sau để build go-quai.

```bash
make go-quai
```
Cấu Hình và Chạy Node
Tạo file cấu hình
Tạo file cấu hình cho node của bạn. file config.toml sẽ được tạo ra trong thư mục ~/.config/go-quai/.

```bash
./build/bin/go-quai config create
```
Mở Tệp Cấu Hình

```bash
nano ~/.config/go-quai/config.toml
```
##Cấu Hình Biến Môi Trường mà bạn có kế hoạch chạy. Bạn có thể tìm thấy các lựa chọn ở đây: https://docs.qu.ai/build/networks#important-links-and-specs.

```bash
environment = 'colosseum'
```
##Cấu Hình Địa Chỉ Coinbase nếu bạn muốn đào Quai và slices
#Đối với Global Node:

```bash
[global node]
coinbases = '0x00a3e45aa16163F2663015b6695894D918866d19,0x01a3e45aa16163F2663015b6695894D918866d19,0x02a3e45aa16163F2663015b6695894D918866d19,0x10a3e45aa16163F2663015b6695894D918866d19,0x11a3e45aa16163F2663015b6695894D918866d19,0x12a3e45aa16163F2663015b6695894D918866d19,0x20a3e45aa16163F2663015b6695894D918866d19,0x21a3e45aa16163F2663015b6695894D918866d19,0x22a3e45aa16163F2663015b6695894D918866d19'
slices = '[0 0],[0 1],[0 2],[1 0],[1 1],[1 2],[2 0],[2 1],[2 2]'
```
#Đối với Single Slice Node:

```bash
[single slice node]
coinbases = '0x00a3e45aa16163F2663015b6695894D918866d19'
slices = '[0 0]'
```
#Đối với Multi Slice Node (Ví dụ 3 slices):

```bash
[multi slice node (3 slices)]
coinbases = '0x00a3e45aa16163F2663015b6695894D918866d19,0x01a3e45aa16163F2663015b6695894D918866d19,0x02a3e45aa16163F2663015b6695894D918866d19'
slices = '[0 0],[0 1],[0 2]'
```
Khởi Chạy Node
Chạy lệnh sau để khởi chạy node.

```bash
./build/bin/go-quai start
```
Dịch Vụ Systemd
Tạo một dịch vụ systemd để quản lý node của bạn.

Tạo File Dịch Vụ
Tạo file dịch vụ quai-node.service với nội dung sau:

```bash
# Lấy user hiện tại
YOUR_USERNAME=$(whoami)

# Lấy đường dẫn đến go-quai
GO_QUAI_PATH=$(readlink -f $(dirname $(which go-quai)))

# Tạo nội dung file dịch vụ và ghi vào /etc/systemd/system/quai-node.service
sudo tee /etc/systemd/system/quai-node.service > /dev/null <<EOL
[Unit]
Description=Quai Network Node
After=network.target

[Service]
User=$YOUR_USERNAME
WorkingDirectory=$GO_QUAI_PATH
ExecStart=$GO_QUAI_PATH/build/bin/go-quai start
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
EOL
```
# Reload systemd và enable dịch vụ

```bash
sudo systemctl daemon-reload
sudo systemctl enable quai-node
```
# Start dịch vụ

```****
sudo systemctl start quai-node
```
# Kiểm tra trạng thái dịch vụ

```bash
sudo systemctl status quai-node
```
