# Kế hoạch di chuyển: Firebase + Angular sang Spring Boot + React

## 1. Cấu trúc dự án

### Cấu trúc thư mục Spring Boot (Backend)
```
src/
└── main/
    ├── java/
    │   └── com/sign/translate/
    │       ├── config/                 # Configuration classes
    │       │   ├── WebConfig.java
    │       │   ├── SecurityConfig.java
    │       │   └── OpenAIConfig.java
    │       ├── controller/             # REST API endpoints
    │       │   ├── TextNormalizationController.java
    │       │   ├── TextToTextController.java
    │       │   ├── SpokenToSignedController.java
    │       │   └── AvatarController.java
    │       ├── service/                # Business logic
    │       │   ├── TextNormalizationService.java
    │       │   ├── TextToTextService.java
    │       │   ├── SpokenToSignedService.java
    │       │   └── AvatarService.java
    │       ├── repository/             # Data access layer
    │       │   └── AvatarRepository.java   # If using database
    │       ├── model/                  # Data transfer objects
    │       │   ├── TextNormalizationRequest.java
    │       │   ├── TextToTextRequest.java
    │       │   ├── SpokenToSignedRequest.java
    │       │   └── AvatarResponse.java
    │       ├── exception/              # Custom exceptions
    │       │   └── TranslationException.java
    │       └── SignTranslateApplication.java
    ├── resources/
    │   ├── application.yml             # Configuration
    │   ├── application-prod.yml
    │   ├── static/                     # Frontend assets (if serving from Spring)
    │   │   └── assets/
    │   │       └── models/             # 3D models
    │   └── templates/                  # HTML templates (if needed)
    └── webapp/                         # Frontend build output (optional)
```

### Cấu trúc thư mục React (Frontend)
```
fontend/src/
├── components/                       # UI Components
│   ├── translate/
│   │   ├── TranslateComponent.tsx
│   │   └── TranslateController.tsx
│   ├── animation/
│   │   ├── AnimationComponent.tsx
│   │   └── AnimationController.tsx
│   ├── text-to-speech/
│   │   └── TextToSpeechComponent.tsx
│   └── video/
│       └── VideoComponent.tsx
├── modules/
│   ├── sign-writing/
│   │   ├── sign-writing.service.ts
│   │   ├── hands.service.ts
│   │   ├── body.service.ts
│   │   └── face.service.ts
│   └── translate/
│       └── translate.service.ts
├── assets/
│   ├── models/                       # 3D models (keep same structure)
│   │   ├── pose-animation/
│   │   │   └── model.json
│   │   ├── hand-shape/
│   │   │   └── model.json
│   │   ├── face-features/
│   │   │   └── model.json
│   │   └── sign-detector/
│   │       └── model.json
│   ├── appearance/                   # Avatar images
│   │   ├── amit.png
│   │   └── maayan.png
│   └── i18n/                         # Localization files
│       ├── en.json
│       ├── vi.json
│       └── ...
├── services/                         # API service clients
│   ├── apiClient.ts
│   ├── textNormalizationService.ts
│   ├── textToTextService.ts
│   └── avatarService.ts
├── utils/                            # Utility functions
│   ├── threejsUtils.ts
│   └── animationUtils.ts
├── App.tsx
└── main.tsx
```

### Tổ chức mô-đun theo nguyên tắc kiến trúc sạch (Clean Architecture)

- **Controller**: Xử lý HTTP requests/responses, chuyển đổi dữ liệu giữa giao diện người dùng và dịch vụ
- **Service**: Chứa logic kinh doanh, gọi các dịch vụ bên ngoài (OpenAI, lưu trữ), xử lý các quy trình
- **Repository**: Quản lý truy cập dữ liệu (nếu sử dụng cơ sở dữ liệu)
- **Model**: Định nghĩa các đối tượng dữ liệu (DTOs)
- **Utils**: Các hàm tiện ích chung

## 2. Lịch trình di chuyển từng giai đoạn

### Giai đoạn 1: Thiết lập dự án & hạ tầng
- [ ] Tạo dự án Spring Boot mới với Spring Initializr (chọn Web, Security, OpenAI)
- [ ] Tạo dự án React mới với Vite hoặc Create React App
- [ ] Thiết lập Dockerfile cho Spring Boot backend
- [ ] Thiết lập docker-compose.yml để chạy cả backend và frontend
- [ ] Thiết lập CI/CD pipeline (GitHub Actions hoặc GitLab CI)
- [ ] Thiết lập cấu hình môi trường (dev, staging, production)
- [ ] Thiết lập hệ thống quản lý cấu hình (Spring Cloud Config hoặc môi trường)

### Giai đoạn 2: Triển khai API backend
- [ ] Triển khai `TextNormalizationController` với endpoint `/api/normalize`
- [ ] Triển khai `TextToTextController` với endpoint `/api/translate`
- [ ] Triển khai `SpokenToSignedController` với endpoint `/api/spoken-to-signed`
- [ ] Triển khai `AvatarController` với endpoint `/api/avatars`
- [ ] Triển khai middleware xác thực JWT
- [ ] Triển khai xử lý lỗi toàn cục
- [ ] Triển khai tài liệu API (Swagger/OpenAPI)
- [ ] Triển khai logging và monitoring

### Giai đoạn 3: Pipeline xử lý văn bản
- [ ] Triển khai `TextNormalizationService` sử dụng Stanford NLP hoặc Apache OpenNLP
- [ ] Triển khai `TextToTextService` tích hợp với Gemini API
- [ ] Triển khai `SpokenToSignedService` để tạo dữ liệu tư thế từ văn bản đã dịch
- [ ] Triển khai bộ đệm (cache) cho các kết quả dịch thuật thường xuyên
- [ ] Triển khai hệ thống kiểm tra chất lượng đầu vào
- [ ] Triển khai giới hạn tốc độ (rate limiting)

### Giai đoạn 4: Di chuyển frontend
- [ ] Di chuyển các thành phần Angular sang React (giữ nguyên logic)
- [ ] Giữ nguyên logic Three.js trong `animation.component.ts`
- [ ] Di chuyển `sign-writing.service.ts`, `hands.service.ts`, `body.service.ts`, `face.service.ts`
- [ ] Di chuyển `translate.service.ts`
- [ ] Cập nhật các dịch vụ API để gọi endpoint Spring Boot thay vì Firebase
- [ ] Kiểm tra tính tương thích của dữ liệu tư thế
- [ ] Tối ưu hóa hiệu suất render 3D
- [ ] Triển khai hệ thống quản lý trạng thái (Redux hoặc Zustand)

### Giai đoạn 5: Di chuyển tài sản & lưu trữ
- [ ] Cập nhật logic tải lên avatar để sử dụng Firebase Storage
- [ ] Cập nhật logic truy cập avatar để sử dụng URL Firebase Storage
- [ ] Thiết lập hệ thống quản lý phiên bản tài sản
- [ ] Triển khai CDN cho tài sản tĩnh
- [ ] Kiểm tra hiệu suất tải tài sản

### Giai đoạn 6: Xác thực
- [ ] Thay thế Unkey bằng Spring Security + JWT
- [ ] Triển khai endpoint `/api/login` để tạo JWT
- [ ] Triển khai endpoint `/api/refresh` để làm mới JWT
- [ ] Triển khai middleware xác thực JWT cho các endpoint bảo vệ
- [ ] Cập nhật frontend để lưu trữ và gửi JWT trong header
- [ ] Triển khai hệ thống quản lý phiên làm việc (session management)

### Giai đoạn 7: Kiểm thử & triển khai
- [ ] Triển khai các bài kiểm thử đơn vị cho backend
- [ ] Triển khai các bài kiểm thử tích hợp cho API
- [ ] Triển khai các bài kiểm thử E2E cho frontend
- [ ] Triển khai kiểm thử hiệu năng (load testing)
- [ ] Triển khai kiểm thử khả năng sử dụng (usability testing)
- [ ] Triển khai hệ thống giám sát (Prometheus + Grafana)
- [ ] Triển khai hệ thống cảnh báo
- [ ] Triển khai hệ thống rollback tự động
- [ ] Triển khai hệ thống giám sát lỗi (Sentry)

## 3. Quyết định công nghệ

### Phụ thuộc Java/Spring (Maven)
```xml
<dependencies>
  <!-- Spring Boot Core -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>

  <!-- OpenAI Integration -->
  <dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>

  <!-- NLP Processing -->
  <dependency>
    <groupId>edu.stanford.nlp</groupId>
    <artifactId>stanford-corenlp</artifactId>
    <version>4.5.7</version>
  </dependency>

  <!-- JWT Authentication -->
  <dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
  </dependency>
  <dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
  </dependency>
  <dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
  </dependency>

  <!-- Database (PostgreSQL) -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
  </dependency>

  <!-- AWS S3 Integration -->
  <dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>s3</artifactId>
    <version>2.20.0</version>
  </dependency>

  <!-- Swagger/OpenAPI -->
  <dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.3.0</version>
  </dependency>

  <!-- Logging -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
  </dependency>
</dependencies>
```

### Thư viện React để giữ hoặc thay thế
- [x] **Giữ nguyên**: `three.js`, `@react-three/fiber`, `@react-three/drei`
- [x] **Giữ nguyên**: `i18next`, `react-i18next`
- [x] **Giữ nguyên**: `axios`
- [x] **Giữ nguyên**: `zustand` hoặc `redux`
- [ ] **Thay thế**: `@angular/core` → `react`
- [ ] **Thay thế**: `@angular/common` → `react`
- [ ] **Thay thế**: `@angular/forms` → `react-hook-form`
- [ ] **Thay thế**: `@angular/router` → `react-router-dom`
- [ ] **Thay thế**: `@angular/http` → `axios`

### Lựa chọn cơ sở dữ liệu
- **PostgreSQL**: Ưu tiên lựa chọn vì:
  - Hỗ trợ tốt cho dữ liệu cấu trúc
  - Hiệu suất tốt cho truy vấn phức tạp
  - Hỗ trợ JSON để lưu trữ dữ liệu tư thế
  - Tích hợp tốt với Spring Boot
  - Hỗ trợ toàn diện cho các hệ thống sản xuất

## 4. Bảng ánh xạ API

| Điểm cuối cũ (Firebase) | Điểm cuối mới (Spring Boot) | Phương thức | Ghi chú |
|------------------------|----------------------------|-----------|---------|
| `/gateway/spoken-to-signed` | `/api/spoken-to-signed` | POST | Chuyển đổi văn bản sang dữ liệu tư thế ngôn ngữ ký hiệu |
| `/gateway/text-normalization` | `/api/normalize` | POST | Chuẩn hóa văn bản đầu vào |
| `/gateway/text-to-text` | `/api/translate` | POST | Dịch giữa các ngôn ngữ nói |
| `/gateway/avatars` | `/api/avatars` | GET | Lấy danh sách avatar |
| `/gateway/avatars/:avatarId` | `/api/avatars/:avatarId` | POST | Tạo avatar mới từ hình ảnh |
| `/gateway/avatars/:avatarId` | `/api/avatars/:avatarId` | DELETE | Xóa avatar |

## 5. Rủi ro & Chiến lược giảm thiểu

### Rủi ro 1: Tính tương thích của định dạng dữ liệu tư thế
- **Mô tả**: Dữ liệu tư thế từ `spoken-to-signed` endpoint cũ có thể không tương thích với logic frontend hiện tại
- **Chiến lược giảm thiểu**:
  - Tạo lớp chuyển đổi (adapter) để ánh xạ định dạng cũ sang định dạng mới
  - Triển khai hệ thống kiểm tra tự động để so sánh đầu ra giữa hai hệ thống
  - Triển khai chế độ tương thích ngược (backward compatibility)
  - Thực hiện kiểm thử kỹ lưỡng với các mẫu dữ liệu thực tế

### Rủi ro 2: Tích hợp Three.js
- **Mô tả**: Các mô hình 3D và logic hoạt ảnh có thể gặp vấn đề khi di chuyển sang React
- **Chiến lược giảm thiểu**:
  - Giữ nguyên các tệp mô hình và logic Three.js không thay đổi
  - Sử dụng `@react-three/fiber` và `@react-three/drei` để giữ tính tương thích
  - Thực hiện kiểm thử từng thành phần riêng biệt
  - Triển khai hệ thống giám sát hiệu suất render

### Rủi ro 3: Độ trễ OpenAI
- **Mô tả**: Gọi OpenAI API có thể gây độ trễ đáng kể trong quy trình dịch thuật
- **Chiến lược giảm thiểu**:
  - Triển khai bộ đệm (cache) cho các kết quả dịch thuật thường xuyên
  - Triển khai hệ thống hàng đợi (queue) để xử lý các yêu cầu đồng thời
  - Triển khai giới hạn tốc độ (rate limiting) để tránh quá tải
  - Triển khai hệ thống fallback khi OpenAI không khả dụng
  - Triển khai hệ thống cảnh báo khi độ trễ vượt ngưỡng

### Rủi ro 4: Di chuyển tài sản lưu trữ
- **Mô tả**: Chuyển đổi từ Firebase Storage sang AWS S3 có thể gây mất dữ liệu hoặc lỗi truy cập
- **Chiến lược giảm thiểu**:
  - Thực hiện sao lưu toàn bộ tài sản trước khi chuyển đổi
  - Triển khai hệ thống đồng bộ hóa hai chiều trong giai đoạn chuyển đổi
  - Triển khai hệ thống kiểm tra tính toàn vẹn dữ liệu
  - Triển khai hệ thống rollback tự động nếu có lỗi

### Rủi ro 5: Xác thực JWT
- **Mô tả**: Thay thế Unkey bằng JWT có thể gây vấn đề về bảo mật hoặc tương thích
- **Chiến lược giảm thiểu**:
  - Triển khai hệ thống kiểm tra bảo mật toàn diện
  - Triển khai hệ thống giám sát xác thực
  - Triển khai hệ thống làm mới JWT tự động
  - Triển khai hệ thống đăng xuất và hủy token

## 6. Định nghĩa hoàn thành

### Giai đoạn 1: Thiết lập dự án & hạ tầng
- [ ] Dự án Spring Boot được tạo và chạy thành công
- [ ] Dự án React được tạo và chạy thành công
- [ ] Dockerfile và docker-compose.yml được thiết lập và chạy thành công
- [ ] CI/CD pipeline được thiết lập và chạy thành công
- [ ] Các môi trường (dev, staging, production) được cấu hình

### Giai đoạn 2: Triển khai API backend
- [ ] Tất cả các endpoint API đã được triển khai và chạy thành công
- [ ] Xác thực JWT đã được triển khai và hoạt động
- [ ] Hệ thống xử lý lỗi toàn cục đã được triển khai
- [ ] Tài liệu API (Swagger/OpenAPI) đã được triển khai và có thể truy cập
- [ ] Logging và monitoring đã được triển khai

### Giai đoạn 3: Pipeline xử lý văn bản
- [ ] `TextNormalizationService` đã được triển khai và hoạt động
- [ ] `TextToTextService` đã được triển khai và hoạt động với OpenAI
- [ ] `SpokenToSignedService` đã được triển khai và tạo dữ liệu tư thế
- [ ] Bộ đệm (cache) đã được triển khai và hoạt động
- [ ] Hệ thống kiểm tra chất lượng đầu vào đã được triển khai
- [ ] Giới hạn tốc độ (rate limiting) đã được triển khai

### Giai đoạn 4: Di chuyển frontend
- [ ] Các thành phần Angular đã được chuyển đổi thành React
- [ ] Logic Three.js đã được giữ nguyên và hoạt động
- [ ] Các dịch vụ sign-writing đã được di chuyển và hoạt động
- [ ] Các dịch vụ API đã được cập nhật để gọi endpoint Spring Boot
- [ ] Dữ liệu tư thế đã được kiểm tra tính tương thích
- [ ] Hiệu suất render 3D đã được tối ưu hóa

### Giai đoạn 5: Di chuyển tài sản & lưu trữ
- [ ] Tài sản đã được chuyển đổi từ Firebase Storage sang AWS S3
- [ ] Logic tải lên avatar đã được cập nhật để sử dụng S3
- [ ] Logic truy cập avatar đã được cập nhật để sử dụng URL S3
- [ ] Hệ thống quản lý phiên bản tài sản đã được triển khai
- [ ] CDN đã được triển khai cho tài sản tĩnh
- [ ] Hiệu suất tải tài sản đã được kiểm tra

### Giai đoạn 6: Xác thực
- [ ] Spring Security + JWT đã được triển khai
- [ ] Endpoint `/api/login` đã được triển khai và hoạt động
- [ ] Endpoint `/api/refresh` đã được triển khai và hoạt động
- [ ] Middleware xác thực JWT đã được triển khai
- [ ] Frontend đã được cập nhật để lưu trữ và gửi JWT
- [ ] Hệ thống quản lý phiên làm việc đã được triển khai

### Giai đoạn 7: Kiểm thử & triển khai
- [ ] Các bài kiểm thử đơn vị đã được triển khai và chạy thành công
- [ ] Các bài kiểm thử tích hợp đã được triển khai và chạy thành công
- [ ] Các bài kiểm thử E2E đã được triển khai và chạy thành công
- [ ] Kiểm thử hiệu năng đã được thực hiện và đạt yêu cầu
- [ ] Kiểm thử khả năng sử dụng đã được thực hiện và đạt yêu cầu
- [ ] Hệ thống giám sát đã được triển khai
- [ ] Hệ thống cảnh báo đã được triển khai
- [ ] Hệ thống rollback tự động đã được triển khai
- [ ] Hệ thống giám sát lỗi đã được triển khai
- [ ] Hệ thống đã được triển khai lên môi trường production
