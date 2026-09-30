# Chạy thử Xray 26.7.28 với Happ

Các gói `v2node-*.zip` chứa binary, GeoIP/GeoSite đầy đủ, cấu hình mẫu,
manifest SHA-256 và hướng dẫn này. Không cần cài Go trên VPS.

## Chọn gói

Chạy `uname -m`: `x86_64` dùng `v2node-linux-64.zip`, `aarch64` dùng
`v2node-linux-arm64-v8a.zip`. Mỗi archive có file `.sha256` đi kèm;
đặt chúng cùng thư mục rồi chạy `sha256sum -c v2node-linux-64.zip.sha256`.

## Chạy một node thử riêng

1. Tạo node mới trên panel, dùng cổng chưa bị chiếm; gán nhóm cho tài khoản
   thử. Với Trojan TLS trực tiếp, mở cổng TCP của node. Với Hysteria2/TUIC,
   mở cổng UDP phù hợp. Domain, SNI, giao thức và TLS được cấu hình ở panel.
2. Giải nén gói vào thư mục riêng, không ghi đè binary/config của node đang chạy:

```sh
mkdir -p ~/v2node-test
unzip v2node-linux-64.zip -d ~/v2node-test
cd ~/v2node-test
chmod +x v2node
./v2node version
cp config.example.json config.json
chmod 600 config.json
```

3. Sửa `config.json`: nhập `ApiHost`, `NodeID` và `ApiKey` thật của node thử.
   Không đăng công khai API key hoặc link subscription. Chứng chỉ/key đã
   cấu hình trên panel phải tồn tại trên VPS và tiến trình phải đọc được.
4. Chạy foreground để xem log (dùng sudo nếu cần quyền đọc chứng chỉ hoặc
   bind cổng dưới 1024):

```sh
./v2node server -c ./config.json
```

`version` phải báo `Xray Core: 26.7.28`. Giữ nguyên `geoip.dat` và
`geosite.dat` cạnh binary; Xray tự tìm dữ liệu trong thư mục executable.
Nhấn Ctrl+C để dừng node thử. Trên Windows dùng `v2node.exe` tương ứng.

## TLS và Happ

- Bước đầu có thể dùng Trojan + chứng chỉ CA hợp lệ, đúng SNI và chain.
- Với chứng chỉ tự ký, chờ node báo fingerprint lên panel và cập nhật
  subscription để lấy pin SHA-256. Không bật `allowInsecure`.
- Với WSS do Nginx kết thúc TLS: bật TLS tại Nginx trên panel, giữ backend
  private, đúng WS path; fingerprint phải thuộc chứng chỉ Nginx phục vụ.
  Không tạo lại chứng chỉ khi chỉ đổi binary.
- Với panel v2Pro, dùng thay đổi subscription trong nhánh
  `upgrade/xray-26.7.28` để loại bỏ cờ TLS insecure cũ.
- Trong Happ: thêm subscription từ clipboard/QR hoặc cập nhật subscription
  đang có, chọn node thử, kết nối và kiểm tra IP đầu ra.

Kiểm tra mở website, truyền dữ liệu, ngắt/kết nối lại, online users và lưu
lượng trên panel. Đối chiếu log khi gặp lỗi API, TLS hoặc timeout. Chỉ có
ping/latency thành công chưa chứng minh proxy truyền dữ liệu được.

## Dữ liệu và bản phát hành

`GEODATA.json` ghi nguồn, commit và SHA-256 của cả hai file dữ liệu;
`PACKAGE.json` ghi nền tảng, phiên bản, commit source và SHA-256 payload.
Dữ liệu lấy từ https://github.com/Loyalsoldier/v2ray-rules-dat, không phải
file mẫu hay dữ liệu rút gọn. Tham khảo repo nguồn về các nguồn dữ liệu
và điều kiện sử dụng của chúng.

Bản chính thức hiện tại là `v0.3.7`. Chi tiết migration và rollback:
`xray-26.7.28-upgrade.md`.
