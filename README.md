# Deluged-Service-Raspberry-Pi
Cài đặt Deluge và Deluge-Web lên Raspberry Pi dưới dạng deamon và tự động khởi chạy sau khi khởi động.

Deluge là một mã nguồn mở BitTorrent đa nền tảng được viết bằng ngôn ngữ Python. Chúng ta cần cài đặt 2 ứng dụng chính deluged và deluge-web. Bạn có thể tải file từ torrent và lưu trực tiếp lên thẻ nhớ đang chạy Raspberry Pi OS. Tuy nhiên, nếu sử dụng một USB 3.0 hoặc ổ cứng di động, tốc độ sẽ được cải thiện rất nhiều. 

Hướng dẫn chi tiết:

1. Update Raspberry Pi lên bản mới nhất với
```
sudo apt update
sudo apt upgrade
```
2. Cài đặt ứng dụng
```
sudo apt install deluged deluge-console deluge-web
```
3. Chạy deamon deluged và đóng để ứng dụng tự tạo file cấu hình
```
deluged
killall deluged
```
4. Tạo user để quản lý Deluge thông qua việc thay đổi file ~/.config/deluge/auth. Với dòng lệnh bên dưới, chúng ta sẽ tạo 1 user có tên pi, mật khẩu là raspberry, cấp độ 10 (admin).
```
echo "pi:raspberry:10" >> ~/.config/deluge/auth
```
5. Bật chế độ điều khiển từ xa với deluge-console

```
deluge-console "config -s allow_remote True"
deluge-console "config allow_remote"
```
6. Cấu hình tạo dịch vụ để deluged và deluge-web tự động chạy trên Raspberry Pi OS
- Tạo mới 1 file deluged.service với Nano
```
sudo nano /etc/systemd/system/deluged.service
```
Copy và dán nội dung này vào
```
[Unit]
Description=Deluge Bittorrent Client Daemon
Documentation=man:deluged
After=network-online.target

[Service]
Type=simple
UMask=007

ExecStart=/usr/bin/deluged -d

Restart=on-failure

# Time to wait before forcefully stopped.
TimeoutStopSec=300

[Install]
WantedBy=multi-user.target

```
Chạy các dòng lệnh sau để chạy deluged dưới dạng deamon

```

sudo systemctl enable /etc/systemd/system/deluged.service
sudo systemctl start deluged
sudo systemctl status deluged
```

- Tạo mới 1 file deluge-web.service với Nano
```
sudo nano /etc/systemd/system/deluge-web.service
```
Copy và dán nội dụng bên dưới
```
[Unit]
Description=Deluge Bittorrent Client Web Interface
Documentation=man:deluge-web
After=network-online.target deluged.service
Wants=deluged.service

[Service]
Type=simple
UMask=027

ExecStart=/usr/bin/deluge-web

Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Chạy các dòng lệnh sau để khởi chạy deluge-web dưới dạng deamon
```
sudo systemctl enable /etc/systemd/system/deluge-web.service
sudo systemctl start deluge-web
sudo systemctl status deluge-web
```
Sau khi hoàn tất, bạn có thể khởi động lại Raspberry Pi để kiểm tra

7. Tiếp tục cấu hình Deluge thông qua trình duyệt trên Raspberry Pi với 127.0.0.1:8112 hoặc từ một máy tính khác với địa chỉ IP:8112

Mật khẩu Deluge Web mặc định là 

>deluge

Bạn có thể tìm thấy video hướng dẫn toàn bộ quá trình cài đặt ở bên dưới:

> https://www.facebook.com/CytronTech.vn/posts/244651807666371

Ngoài ra, các sản phẩm trong video có thể tìm thấy tại [Cửa hàng trực tuyến của Cytron](https://cytrontech.vn/?tracking=vngh)

Chúc thành công!





