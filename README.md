# [Tên Service] – README

---

## 1. 🧭 Service Overview

- **Mục tiêu chính**: Dịch vụ này xử lý nghiệp vụ gì?
- **Vị trí trong hệ thống**: Public/Private API? Do Internal Gateway nào quản lý?
- **Được gọi từ đâu?** (client/app/API Gateway/internal?)

> 📌 Ví dụ: Service quản lý đơn hàng nội bộ, private, được gọi qua Internal Gateway System C.

---

## 2. 📡 Service Integration Map

- **Upstream**: Các hệ thống nào gọi tới service này (Gateway, Backend, Cron...)?
- **Downstream**: Gọi service nào? Gửi Kafka/topic gì?
- **External dependency**: Gọi API bên ngoài nào (nếu có)?

> 🗺️ Có thể đính kèm sơ đồ PNG: `./docs/service-diagram.png`

---

## 3. 🛠️ API Endpoints

| Method | Endpoint             | Mô tả ngắn               |
|--------|----------------------|--------------------------|
| GET    | /api/v1/orders       | Lấy danh sách đơn hàng   |
| POST   | /api/v1/orders       | Tạo đơn hàng mới         |

> 📎 Chi tiết input/output & status code → xem OpenAPI Spec

---

## 4. 📦 Dependencies

- **Service nội bộ**: AuthService, InventoryService
- **Infra**: PostgreSQL, Redis, Kafka (topic: `order.created`)
- **Third-party**: Stripe API

---

## 5. 🔄 Business Logic Flow

1. Kiểm tra quyền từ token
2. Validate dữ liệu đơn hàng
3. Ghi DB, update Redis
4. Gửi Kafka (nếu thành công)
5. Trả response cho Gateway

---

## 6. ⚙️ Configuration & Deployment (CI/CD via GitLab)

### 6.1 Env Variables

| Key             | Mô tả                | Default     |
|------------------|----------------------|-------------|
| DB_URL           | Kết nối DB           | (secret)    |
| REDIS_HOST       | Redis Cache          | 127.0.0.1   |
| KAFKA_BROKER     | Kafka Cluster        | kafka:9092  |
| ENVIRONMENT      | Môi trường           | staging     |

### 6.2 GitLab CI/CD Notes

- File `.gitlab-ci.yml` đã cấu hình:
  - ✅ Build image
  - ✅ Unit test & Linter
  - ✅ Deploy staging/prod
  - ✅ Publish Swagger to Portal

---

## 7. 🔐 API Governance Compliance

- ✅ Được kiểm soát qua Gateway: **System C**
- ✅ Có Logging / ACL / Rate Limit / Monitoring
- ✅ OpenAPI validated
- ✅ Security Header: Bearer Token (JWT)

---

## ❗ 8. Error Code & Xử lý lỗi

| Mã lỗi | Mô tả kỹ thuật           | HTTP Code | Retry | User Message                                           | PIC     | Liên hệ                |
|--------|---------------------------|-----------|-------|--------------------------------------------------------|---------|-------------------------|
| OR001  | Thiếu access token        | 401       | ❌    | Bạn chưa đăng nhập.                                   | Backend | backend-team@company.com |
| OR002  | Lỗi khi tạo đơn hàng mới  | 500       | ✅    | Đã xảy ra lỗi khi xử lý đơn hàng, vui lòng thử lại.   | DevOps  | devops@company.com       |
| OR003  | Không tìm thấy đơn hàng   | 404       | ❌    | Không tìm thấy đơn hàng yêu cầu.                      | QA      | qa@company.com           |
| OR004  | Redis timeout             | 502       | ✅    | Hệ thống đang quá tải, vui lòng thử lại sau.          | DevOps  | techops@company.com      |
| OR005  | Kafka không phản hồi      | 504       | ✅    | Kết nối hệ thống thất bại, vui lòng thử lại.          | DevOps  | devops@company.com       |

> 📂 Xem chi tiết định nghĩa & hướng xử lý tại: `./docs/error_code.md`

---

## 9. 📚 Reference & Docs

- 🔹 [OpenAPI Spec](./openapi.yaml)
- 🔹 [error_code.md](./docs/error_code.md)
- 🔹 [Coding Convention](../docs/API_Coding_Convention.md)
- 🔹 [CI/CD Status - GitLab](https://gitlab.company.com/project/service/pipelines)

---

## 10. 🛠️ Troubleshooting

| Tình huống                 | Cách xử lý                                      |
|----------------------------|-------------------------------------------------|
| Không thấy trace          | Kiểm tra `SetSampler`, env, collector endpoint  |
| Collector không kết nối   | Kiểm tra port, restart Otel Collector           |
| Lỗi deploy CI/CD          | Kiểm tra stage test hoặc missing secrets        |

---

## 11. 👤 Contact

| Vai trò     | Họ tên     | Email                 |
|-------------|------------|------------------------|
| Dev Owner   | Trần Văn A     | abc@company.com     |
| DevOps Lead | Nguyễn Văn B | devops@company.com   |

---

📌 **Ghi chú**: Tài liệu này nên được cập nhật mỗi khi service thay đổi đáng kể về logic nghiệp vụ, API hoặc lỗi xử lý.  
✅ Nếu có Pull Request ảnh hưởng đến phần API hoặc config, hãy đảm bảo cập nhật luôn `README.md`.

