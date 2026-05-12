# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm: Nhóm 11
- Lớp: CNTT_17-09
- Thành viên:Nguyễn Ánh Dương 
- Service nhóm phụ trách:IOT Ingestion
- Sản phẩm tổng thể của lớp:

## 2. Actor

Ai tương tác với hệ thống/service?
- IoT Devices/Sensors: Các thiết bị phần cứng (nhiệt độ, độ ẩm, GPS,...) gửi dữ liệu về.
- System Administrator: Người cấu hình các giao thức kết nối và giám sát lưu lượng dữ liệu.
- Other Microservices: Các dịch vụ xử lý dữ liệu sau khi tiếp nhận.

## 3. System Boundary

Nhóm em xây phần nào?

Phần nhóm kiểm soát:
- Xây dựng các Endpoint tiếp nhận dữ liệu đa giao thức (HTTP/MQTT).
- Bộ lọc dữ liệu (Filter) để loại bỏ các gói tin rác hoặc không đúng cấu trúc.
- Cơ chế xác thực thiết bị (Device Authentication) bằng Token hoặc API Key.
- Cơ chế đệm dữ liệu (Buffering) trước khi đẩy vào hệ thống xử lý tiếp theo

Phần nhóm chỉ tích hợp:
- Message Broker (Kafka/RabbitMQ): Nhóm đẩy dữ liệu vào đây để các service khác tiêu thụ.
- Identity Provider: Hệ thống quản lý tài khoản/thiết bị chung của lớp để đối soát quyền truy cập.

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?
- Tiếp nhận kết nối đồng thời từ hàng nghìn thiết bị (High Concurrency).
- Chuẩn hóa dữ liệu thô từ các định dạng khác nhau về định dạng JSON chung của hệ thống.
- Gắn nhãn thời gian (Timestamping) chính xác thời điểm dữ liệu đi vào hệ thống.
- Đảm bảo dữ liệu không bị mất mát trong quá trình tiếp nhận.

Service KHÔNG làm gì?
- Không lưu trữ dữ liệu vào Database lâu dài (đó là việc của Storage Service).
- Không thực hiện tính toán logic phức tạp hoặc đưa ra cảnh báo (đó là việc của Processing Service).
- Không điều khiển ngược lại thiết bị (Actuation).

## 5. Input / Output

### Input

- Các gói tin thô (Raw Payloads) từ cảm biến.
- Thông tin định danh thiết bị (Device ID) và mã bảo mật (Access Token).
- Giao thức truyền tải: MQTT Topic hoặc HTTP POST Request.

### Output

- Dữ liệu đã được làm sạch và chuẩn hóa (Structured JSON).
- Log trạng thái tiếp nhận (Success/Fail) cho mục đích giám sát.
- Message gửi vào hàng chờ (Queue) để kích hoạt các service phía sau.

## 6. API dự kiến

| Method | Endpoint          | Mục đích                    |
| ------ | ----------------- | --------------------------- |
| GET    | /health           | Kiểm tra trạng thái service |
| POST   | /api/iot/data     | Tiếp nhận dữ liệu IoT       |
| POST   | /api/iot/validate | Xác thực thiết bị           |
| GET    | /api/iot/status   | Kiểm tra trạng thái kết nối |
| POST   | /api/iot/publish  | Đẩy dữ liệu vào queue       |

## 7. Phụ thuộc service khác

Service này gọi đến service nào?
- Device Registry Service: Để kiểm tra xem ID thiết bị gửi lên có được phép hoạt động không.
- Message Broker: Để chuyển giao dữ liệu sau khi "tiêu hóa" xong.

Service nào gọi đến service này?
- IoT Devices: Chủ động đẩy dữ liệu lên qua Internet.
- API Gateway: Đóng vai trò lớp bảo mật và định tuyến yêu cầu từ bên ngoài vào service.

## 8. Sơ đồ minh họa

![alt text](image.png)
