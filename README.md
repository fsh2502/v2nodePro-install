# v2nodePro — hướng dẫn cài đặt và sử dụng

v2nodePro là chương trình chạy node kết nối với panel V2Board tương thích. Tên chương trình và dịch vụ sau khi cài là `v2node`. Bản phát hành mới nhất là [v0.3.7](https://github.com/fsh2502/v2nodePro-install/releases/tag/v0.3.7), sử dụng Xray Core 26.7.28.

Repository này chứa script cài đặt, cấu hình mẫu và tài liệu. Các gói chạy nằm trong [GitHub Releases](https://github.com/fsh2502/v2nodePro-install/releases); repository cài đặt không chứa mã Go của ứng dụng.

## Trước khi cài đặt

- Chuẩn bị máy chủ Linux và chạy lệnh cài đặt bằng quyền `root` (có thể dùng `sudo -i` trước khi chạy). Script cài đặt hỗ trợ Linux x86_64, ARM64 và s390x.
- Chuẩn bị địa chỉ panel V2Board tương thích, Node ID và API key của node. Panel cần hỗ trợ API mà v2nodePro sử dụng.
- Nếu cần dùng WSS qua Nginx, chuẩn bị domain trỏ đến máy chủ và quyền quản lý cổng `443`.

## Cài đặt nhanh

Chạy lệnh sau trên máy chủ Linux bằng quyền `root`:

```bash
wget -N https://raw.githubusercontent.com/fsh2502/v2nodePro-install/main/script/install.sh && bash install.sh
```

Script tìm bản phát hành mới nhất, tải gói phù hợp cùng file SHA-256, kiểm tra checksum rồi mới thay bản đang cài. Khi cần chọn bản cụ thể, tải script và chạy `bash install.sh v0.3.7` thay cho lệnh cài nhanh.

Lần cài đầu, script hỏi có muốn tạo `/etc/v2node/config.json` hay không. Chọn `y` và nhập địa chỉ panel, Node ID, API key. Nếu bỏ qua, file cấu hình mẫu sẽ được tạo; hãy sửa các giá trị mẫu trước khi dùng node.

## Cài đặt và quản lý nâng cao

Menu quản lý máy chủ có lệnh cài node, thêm hoặc xóa node, cấu hình WSS và một số công cụ khác:

```bash
wget -N https://raw.githubusercontent.com/fsh2502/v2nodePro-install/main/script/caidatserver.sh && bash caidatserver.sh
```

Trong menu, chọn **1** để cài v2nodePro, **8** để thêm node vào cấu hình, **9** để xóa node, **10** để cấu hình WSS trên cổng `443`, **11** để xem WSS đã cấu hình và **12** để xóa cấu hình WSS. Khi dùng WSS, domain phải trỏ đúng máy chủ và Nginx cần dùng đúng chứng chỉ cho domain đó.

## Cấu hình node

File cấu hình nằm tại `/etc/v2node/config.json`. Ví dụ cho một node:

```json
{
  "Log": {"Level": "info", "Output": "", "Access": "none"},
  "Nodes": [
    {
      "ApiHost": "https://panel.example.com",
      "NodeID": 123,
      "ApiKey": "THAY_BANG_API_KEY_CUA_NODE",
      "Timeout": 15
    }
  ],
  "PprofPort": 0
}
```

Thay `ApiHost`, `NodeID` và `ApiKey` bằng thông tin trên panel. `Timeout` tính bằng giây; `PprofPort: 0` tắt cổng debug. Có thể thêm nhiều mục vào `Nodes` để chạy nhiều node. Không đưa file cấu hình thật hoặc API key lên GitHub.

Sau khi cài, chạy `v2node config` để sửa file bằng `vi` và khởi động lại dịch vụ. Lệnh `v2node generate` tạo lại file cấu hình từ đầu và **ghi đè nội dung hiện có**; chỉ dùng khi muốn thay toàn bộ cấu hình.

## Kiểm tra và sử dụng

| Lệnh | Tác dụng |
| --- | --- |
| `v2node` | Mở menu quản lý. |
| `v2node status` | Kiểm tra trạng thái dịch vụ. |
| `v2node log` | Xem nhật ký khi node không kết nối hoặc khởi động lỗi. |
| `v2node restart` | Khởi động lại sau khi sửa cấu hình. |
| `v2node start` / `v2node stop` | Khởi động hoặc dừng dịch vụ. |
| `v2node enable` / `v2node disable` | Bật hoặc tắt khởi động cùng hệ thống. |
| `v2node version` | Xem phiên bản đang cài. |
| `v2node update` | Cập nhật lên bản phát hành mới nhất. |
| `v2node update v0.3.7` | Cài hoặc cập nhật đúng phiên bản được chỉ định. |

Nếu node không hoạt động, kiểm tra `v2node status`, sau đó xem `v2node log`; đối chiếu địa chỉ panel, Node ID và API key trong `/etc/v2node/config.json`. Trên hệ thống dùng `systemd`, cũng có thể xem `systemctl status v2node` và `journalctl -u v2node.service -e --no-pager`.

## Tải gói thủ công

Trang [Releases](https://github.com/fsh2502/v2nodePro-install/releases) có ZIP cho Linux và các nền tảng khác. Ví dụ, Linux x86_64 dùng `v2node-linux-64.zip`, Linux ARM64 dùng `v2node-linux-arm64-v8a.zip`. Đặt ZIP và file `.sha256` tương ứng trong cùng thư mục rồi kiểm tra bằng `sha256sum -c v2node-linux-64.zip.sha256` trước khi giải nén. Gói chứa binary, GeoIP/GeoSite, cấu hình mẫu, script và tài liệu. Script cài đặt tự động ở trên chỉ dành cho Linux.

## Mã nguồn và giấy phép

Xem [thông báo mã nguồn](SOURCE-NOTICE.md) và [giấy phép MPL 2.0](LICENSE). Khi phát hành binary, người nhận vẫn cần có cách tiếp cận mã nguồn tương ứng của các thành phần thuộc MPL 2.0, kể cả khi repository mã nguồn chuyển sang private.
