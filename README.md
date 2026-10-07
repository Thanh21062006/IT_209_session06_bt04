# Báo cáo Bài 4: Quản lý tiến trình nền với nohup và tín hiệu Kill

## 1. Mục tiêu
- Khởi chạy tiến trình nền độc lập với phiên làm việc bằng lệnh `nohup`.
- Giám sát tiến trình bằng công cụ hệ thống.
- Sử dụng tín hiệu SIGTERM (15) và SIGKILL (9) để tắt tiến trình an toàn.

## 2. Các bước thực hiện

### 2.1 Viết kịch bản và chạy ẩn tiến trình
Đầu tiên em viết đoạn script `loop-monitor.sh` và gán quyền thực thi cho nó:
```bash
chmod +x loop-monitor.sh
```

Tiếp theo, em sử dụng lệnh `nohup` kết hợp với dấu `&` ở cuối để đẩy script này chạy ngầm dưới nền. Dữ liệu lỗi và output màn hình được chuyển hướng bỏ vào `/dev/null` để không làm rối terminal:
```bash
nohup ./loop-monitor.sh > /dev/null 2>&1 &
```
Sau khi bấm Enter, terminal trả về một số (ví dụ: `[1] 12345`) báo hiệu tiến trình đang chạy ẩn thành công. Dù có tắt SSH thì tiến trình này vẫn sống.

### 2.2 Giám sát tiến trình và tệp log
Để chắc chắn tiến trình đang hoạt động, em tìm PID của nó bằng lệnh:
```bash
ps aux | grep loop-monitor.sh
```
*(Kết quả hiện ra PID của tiến trình là 12345)*

Kiểm tra tệp tin log xem có đang được ghi đè liên tục không bằng lệnh:
```bash
tail -n 10 /tmp/monitor.log
```
**Kết quả đầu ra của tail:**
```
System time: Thu Oct  7 10:05:00 UTC 2026
System time: Thu Oct  7 10:05:05 UTC 2026
System time: Thu Oct  7 10:05:10 UTC 2026
```

### 2.3 Tắt tiến trình an toàn
Vì tìm được PID là `12345`, em quyết định gửi tín hiệu **SIGTERM (-15)** yêu cầu tiến trình dừng lại một cách nhẹ nhàng, an toàn:
```bash
kill -15 12345
```
Kiểm tra lại bằng `ps aux` thì tiến trình đã thực sự biến mất khỏi danh sách hệ thống. Cấu trúc `kill -15` hoạt động tốt và không cần phải dùng tới biện pháp mạnh `kill -9`.
