[English](README.md) | [Tiếng Việt](README.vi.md) | [简体中文](README.zh-CN.md)

# Betterone Console Server

Triển khai hoàn toàn bằng phần mềm, không yêu cầu phần cứng chuyên dụng. Có thể chạy trên máy tính thông thường hoặc Raspberry Pi.

Truy cập các cổng nối tiếp vật lý (serial port) trên máy tính thông thường hoặc Raspberry Pi thông qua SSH, Web Terminal, Telnet và Raw TCP, đồng thời cung cấp REST API để quản lý cấu hình và phiên làm việc. Hỗ trợ tối đa 48 console; phiên bản miễn phí cho phép sử dụng các cổng từ 1–2; dung lượng cấp phép thương mại và phạm vi bảo trì tuân theo giấy phép (license).

## Bắt đầu nhanh

Phiên bản dịch vụ (Service) cho Windows: chạy `betterone-cs_<phiên bản>_Windows_x86_64_setup.exe`; đối với Linux: cài đặt gói deb/rpm tương ứng với kiến trúc hệ thống (dịch vụ sẽ tự động khởi động sau khi cài đặt). Khi mở giao diện web lần đầu tiên, hãy thiết lập mật khẩu quản trị viên ngay; không có mật khẩu mặc định. Chi tiết về cài đặt và nâng cấp xem tại [Sổ tay hướng dẫn người dùng](docs/customer/cn/USER_MANUAL.md).

Đối với bản Portable (không cần cài đặt), sau khi giải nén:

```sh
./betterone-cs --init-config
# Trên Linux, nếu người dùng thông thường không thể liên kết cổng 80, hãy bỏ chú thích http_port trong config/settings.toml và đổi thành 8080.
./betterone-cs
```

Trên Windows, sử dụng `betterone-cs.exe`. Mặc định ở chế độ portable, cấu hình và dữ liệu được lưu trong thư mục `config/` và `data/` bên cạnh chương trình. Trong tệp `settings.toml` được tạo ra, tất cả các mục đều là giá trị mặc định được chú thích (comment); chỉ những mục được bỏ chú thích mới có hiệu lực. Các thiết lập về địa chỉ Web, HTTPS và kiểm toán (audit) được sửa đổi trên trang web sẽ được lưu trong cơ sở dữ liệu và không ghi ngược lại vào tệp này. Truy cập `http://<IP-máy-chủ>:<cổng>/`, đặt mật khẩu quản trị viên dài tối thiểu 8 ký tự (không được chỉ chứa toàn chữ cái hoặc chỉ chứa toàn số), sau đó tiến hành liên kết cổng nối tiếp trên giao diện web.

## Truy cập & Bảo mật

| Phương thức truy cập | Cổng mặc định | Trạng thái mặc định |
| --- | --- | --- |
| Quản lý Web / API / Web Terminal | 80 | Bật; đường dẫn WebSocket console `/console/N`, mặc định yêu cầu đăng nhập |
| SSH | 6000 + N | Bật; mặc định yêu cầu xác thực bằng mật khẩu hoặc khóa công khai (public key) |
| Telnet | 7000 + N | Tắt; sau khi bật mặc định yêu cầu đăng nhập |
| Raw TCP | 5000 + N | Tắt; sau khi bật mặc định yêu cầu đăng nhập |

`N` là số thứ tự console (1–48). Địa chỉ lắng nghe mặc định cho cấu hình mới tạo là `0.0.0.0`. Telnet và Raw TCP mặc định không khởi chạy lắng nghe; vui lòng kiểm tra lại địa chỉ lắng nghe trước khi bật.

Quản trị viên có thể thiết lập riêng cho từng console xem có yêu cầu đăng nhập đối với 4 giao thức này hay không. Khi tắt yêu cầu đăng nhập, bất kỳ ai truy cập được vào địa chỉ lắng nghe của giao thức đó đều có thể sử dụng console trực tiếp. Telnet và Raw TCP không được mã hóa; ngay cả khi bật đăng nhập, tài khoản và mật khẩu vẫn được truyền dưới dạng văn bản thuần (plaintext). Khi cần mã hóa, hãy sử dụng SSH, HTTPS Web Terminal hoặc đường hầm (tunnel) được bảo vệ. Không để thiết bị kết nối vào mạng không tin cậy khi chưa thiết lập mật khẩu quản trị viên.

Mỗi giao thức cũng có thể kiểm soát riêng các thông báo phụ trợ (mặc định bật cho SSH và WebSocket, mặc định tắt cho Telnet và Raw TCP). Khi tắt, hệ thống sẽ không xuất các thông tin giải thích phía máy chủ như trạng thái cổng nối tiếp, quyền ghi; dữ liệu cổng nối tiếp cũng như các lời nhắc tài khoản/mật khẩu cần thiết cho đăng nhập vẫn được giữ lại.

## Thao tác Cổng nối tiếp & Cấu hình hàng loạt

Tại trang Cổng nối tiếp vật lý, các cổng đã liên kết có thể mở console tương ứng; các cổng chưa liên kết có thể được quản trị viên hoặc kỹ sư mở trực tiếp qua "Mở cổng nối tiếp". Mở trực tiếp sẽ không lưu cài đặt và chỉ cho phép một phiên kết nối trực tiếp tại một thời điểm trên cùng một cổng; sau khi liên kết vào console, các thông số cổng hiện tại sẽ được lưu cùng. Console hỗ trợ chỉnh sửa hàng loạt các thông số cổng nối tiếp, bật/tắt giao thức, yêu cầu đăng nhập, thông báo phụ trợ, giới hạn kết nối và chế độ ghi.

Trang Hệ thống hỗ trợ xuất ghi chú, liên kết phần cứng, thông số cổng nối tiếp, bật/tắt giao thức, yêu cầu đăng nhập, thông báo phụ trợ, giới hạn kết nối, chế độ ghi và địa chỉ lắng nghe của toàn bộ 48 console; không bao gồm người dùng, API Key, giấy phép hoặc cài đặt hệ thống. Quá trình nhập (import) cung cấp hai phương thức:

- **Cấu hình hàng loạt** (Mặc định): Áp dụng cài đặt chung theo số thứ tự console, giữ nguyên định danh COM/TTY, vị trí USB/nhận diện phần cứng, IP lắng nghe và cổng của máy đích.
- **Khôi phục hoàn toàn**: Ghi đè toàn bộ cấu hình cổng, thích hợp cho việc khôi phục trên cùng một máy.

Ngay cả các thiết bị cùng hãng cùng mẫu vẫn có thể có tên COM, số sê-ri USB, vị trí vật lý và IP máy khác nhau, do đó không thể sao chép nguyên bản bản sao lưu đầy đủ sang máy khác; trước khi áp dụng trên các máy khác nhau, cần xác nhận cổng nối tiếp đích có hỗ trợ các thông số đã chọn. Quá trình nhập có thể làm gián đoạn các kết nối hiện có.

Mục tiêu phát hành hỗ trợ Windows amd64, cùng với Linux amd64, arm64, armv7 (GOARM=7). Lựa chọn theo kiến trúc hệ điều hành đích; cổng USB nối tiếp vật lý, trình điều khiển (driver) và hiệu quả cài đặt trên hệ thống đích cần được kiểm tra thực tế trên thiết bị.

## Cấp phép & Cấu hình

Các gói giấy phép thương mại được bán trực tiếp hiện tại gồm 3 mức: **8P, 16P, 48P**; các mức khác không bán trực tiếp. Phiên bản miễn phí có thể sử dụng console 1–2; dung lượng thương mại thực tế dựa theo giấy phép được cấp.

Giấy phép có giá trị vĩnh viễn, thời hạn bảo trì chỉ giới hạn ngày phát hành phần mềm được phép sử dụng, không phải là ngày hết hạn chạy phần mềm theo thời gian thực. Thời hạn bảo trì được tính theo từng thiết bị; các thao tác kích hoạt trực tuyến ACT, gia hạn RNW, yêu cầu ngoại tuyến và nhập tệp đều được thực hiện trên trang Cấp phép. Nhận diện thiết bị sử dụng cơ chế hardware/install/generated, không sử dụng địa chỉ MAC của card mạng.

Tệp TOML chỉ cần ghi các trường muốn ghi đè; các trường không ghi sẽ sử dụng giá trị mặc định của chương trình. Các bản dựng chính thức sử dụng địa chỉ máy chủ cấp phép và cổng xử lý trang chủ được tích hợp sẵn (máy chủ cấp phép là `https://lic.cs.betterone.top`), bỏ qua các giá trị ghi đè cũng như giá trị rỗng trong cấu hình. Chỉ các bản dựng thử nghiệm `-tags tst` mới cho phép ghi đè `license_server`, `license_portal_url`, `license_offline_url` (giá trị rỗng có thể tắt tính năng hoặc lối vào tương ứng). Trang web chính thức của dự án cố định là `https://cs.betterone.top`. Nâng cấp bản cài đặt sẽ khởi động lại và ngắt kết nối các phiên; gỡ cài đặt vẫn giữ lại cấu hình và dữ liệu, việc khôi phục cài đặt gốc cần có sự xác nhận trực tiếp trên máy cục bộ.

## Dự án & Liên hệ

Trang web chính thức: [cs.betterone.top](https://cs.betterone.top)

GitHub: [betterone-top/console-server](https://github.com/betterone-top/console-server)

Email: [betterone-top@hotmail.com](mailto:betterone-top@hotmail.com)
