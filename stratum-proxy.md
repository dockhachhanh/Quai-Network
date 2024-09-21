# Hướng dẫn Cài đặt và Chạy Stratum Proxy trên Quai Network
## Giới thiệu
Hướng dẫn này cung cấp từng bước cài đặt và chạy go-quai-stratum, phiên bản Go của Stratum proxy trên Quai Network. Hướng dẫn này tập trung vào các hệ điều hành Linux và macOS. Hiện tại, việc chạy go-quai-stratum trên Windows hoặc WSL2 chưa được hỗ trợ.

## Thiết lập Môi trường
Để có quá trình cài đặt đơn giản nhất, chúng tôi khuyến nghị cài đặt và chạy go-quai-stratum trên cùng một máy tính mà bạn đang chạy go-quai. Chạy go-quai-stratum trên một máy tính khác chỉ được khuyến nghị cho người dùng nâng cao vì cần có cấu hình mạng bổ sung.

## Cài đặt Các Phụ thuộc
Để chạy một phiên bản của go-quai-stratum, bạn cần cài đặt một số phụ thuộc. Bạn có thể cài đặt các phụ thuộc này bằng trình quản lý gói yêu thích của mình (apt, brew, v.v.).

### 1 - Go v1.21.0+
Cài đặt Snap trên Linux
```bash
# Cài đặt snapd nếu bạn chưa có
sudo apt install snapd

# Cài đặt Go
sudo snap install go --classic
```
Cài đặt trên macOS
```bash
brew install go
```
### 2 - Git, Make, và G++
Cài đặt trên Linux
```bash
# Cài đặt git và make
sudo apt install git make g++
```
Cài đặt trên macOS
```bash
brew install git make gcc
```
### 3 - go-quai-stratum
Sau khi đã cài đặt các phụ thuộc cơ bản, bạn có thể tiến hành clone repo go-quai-stratum.
```bash
git clone https://github.com/dominant-strategies/go-quai-stratum
cd go-quai-stratum
```
Lệnh này sẽ cài đặt nhánh chính vào máy tính của bạn. Trừ khi bạn định phát triển, bạn phải kiểm tra phiên bản phát hành mới nhất.
Bạn có thể tìm thấy phiên bản phát hành mới nhất trên trang phát hành go-quai-stratum https://github.com/dominant-strategies/go-quai-stratum/tags. Sau đó, kiểm tra phiên bản phát hành mới nhất bằng lệnh:
Vd: v0.13.0-rc.0
```bash
#git checkout put-latest-release-her
git checkout v0.13.0-rc.0
```
## Cấu hình
Để chạy proxy Stratum của Quai, bạn cần thực hiện một số cấu hình nhỏ. Bắt đầu bằng cách sao chép file cấu hình ví dụ sang một file cấu hình cục bộ:
```bash
cp config/config.example.json config/config.json
```
Lệnh này sẽ sao chép cấu hình ví dụ và tạo một file cấu hình mới trong thư mục config. Trong file config.json mới tạo, bạn có thể cấu hình các thiết lập mạng và các biến liên quan khác.
## Chạy Proxy
# Build
Trước khi chạy proxy, chúng ta cần build source code. Bạn có thể build bằng Makefile bằng cách chạy lệnh sau:
```bash
make go-quai-stratum
```
## Run
Sau khi đã build xong source code, bạn có thể bắt đầu chạy proxy. Chúng tôi khuyến nghị sử dụng một trình quản lý quy trình như tmux hoặc screen để chạy proxy.
Để chạy proxy, bạn cần chọn một shard để chạy. Bắt đầu proxy bằng cách truyền vào các cổng web socket tương ứng hoặc tên chuỗi cho khu vực và vùng bạn đã chọn để chạy. Chạy bằng tên chuỗi sử dụng lệnh này:
```bash
./build/bin/go-quai-stratum --region=REGION-NAME --zone=ZONE-NAME
```
Hoặc để chạy bằng cổng, sử dụng:
```bash
./build/bin/go-quai-stratum --region=REGION-WS-PORT --zone=ZONE-WS-PORT
```
Chạy proxy sẽ chỉ hoạt động cho các chuỗi mà node của bạn đang xác thực trạng thái. Các node toàn cầu xác thực trạng thái cho tất cả các chuỗi, trong khi các node lát chỉ xác thực trạng thái cho các chuỗi mà bạn chỉ định.
Các tùy chọn khả dụng cho REGION-NAME, ZONE-NAME, REGION-PORT, và ZONE-PORT có thể được tìm thấy bên dưới. Bạn phải chọn một khu vực và vùng tương ứng,
## Các Tùy Chọn cho REGION-NAME, ZONE-NAME, REGION-PORT và ZONE-PORT
Bạn cần chọn một vùng và khu vực tương ứng, ví dụ: paxos và paxos3 hoặc hydra và hydra2.
Nếu bạn đang chạy phiên bản của go-quai-stratum trước v0.9.0-rc.0, bạn sẽ cần truyền vào các cổng web socket tương ứng vì tên chuỗi không được hỗ trợ trước phiên bản này.
| Tên Chuỗi     | Loại  | Chỉ số Chuỗi | Cổng Web Socket | Lệnh Stratum                                          |
|---------------|-------|---------------|------------------|------------------------------------------------------|
| Cyprus        | Vùng  |               | 8579             |                                                      |
| Paxos         | Vùng  |               | 8581             |                                                      |
| Hydra         | Vùng  |               | 8583             |                                                      |
| Cyprus-1      | Khu   | [0 0]         | 8611             | `./build/bin/go-quai-stratum —region=cyprus —zone=cyprus1` |
| Cyprus-2      | Khu   | [0 1]         | 8643             | `./build/bin/go-quai-stratum —region=cyprus —zone=cyprus2` |
| Cyprus-3      | Khu   | [0 2]         | 8675             | `./build/bin/go-quai-stratum —region=cyprus —zone=cyprus3` |
| Paxos-1       | Khu   | [1 0]         | 8613             | `./build/bin/go-quai-stratum —region=paxos —zone=paxos1` |
| Paxos-2       | Khu   | [1 1]         | 8645             | `./build/bin/go-quai-stratum —region=paxos —zone=paxos2` |
| Paxos-3       | Khu   | [1 2]         | 8677             | `./build/bin/go-quai-stratum —region=paxos —zone=paxos3` |
| Hydra-1       | Khu   | [2 0]         | 8615             | `./build/bin/go-quai-stratum —region=hydra —zone=hydra1` |
| Hydra-2       | Khu   | [2 1]         | 8647             | `./build/bin/go-quai-stratum —region=hydra —zone=hydra2` |
| Hydra-3       | Khu   | [2 2]         | 8679             | `./build/bin/go-quai-stratum —region=hydra —zone=hydra3` |

### Lưu ý: 
Không mở các cổng web socket trên trừ khi máy khai thác của bạn nằm trên một mạng khác với nút/stratum của bạn (và ngay cả trong trường hợp đó, hãy chắc chắn chỉ mở cổng cho máy cần thiết). Bạn có thể đặt mạng cục bộ của bạn vào rủi ro bảo mật.
Mặc định, proxy lắng nghe kết nối của thợ mỏ trên cổng 3333. Bạn có thể thay đổi cổng mà proxy lắng nghe bằng cách truyền vào thông qua tham số --stratum trong lệnh chạy nếu bạn muốn.
```bash
./build/bin/go-quai-stratum --region=REGION-NAME --zone=ZONE-NAME --stratum=LISTENING-PORT
```
Thay đổi cổng lắng nghe của proxy là hữu ích khi chạy nhiều proxy trên một nút toàn cầu duy nhất. Nếu bạn chỉ khai thác trên một shard, không cần thiết phải thay đổi cổng lắng nghe.
Proxy sẽ bắt đầu phát luồng log tới terminal, trông tương tự như dưới đây.
