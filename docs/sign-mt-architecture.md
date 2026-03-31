Based on the sign.mt architecture analysis in sign-mt-architecture.md, create a detailed migration plan file named migration-plan.md for converting the existing Firebase + Angular project to a Spring Boot + React stack.
The plan should cover:
1. Project Structure

Proposed folder structure for both Spring Boot backend and React frontend
Package/module organization following clean architecture principles (Controller → Service → Repository)

2. Phase-by-Phase Migration Roadmap
   Break the migration into clear phases with estimated effort:

Phase 1: Project setup & infrastructure (Spring Boot init, React init, Docker, CI/CD)
Phase 2: Backend API implementation (REST controllers replicating Firebase Function endpoints)
Phase 3: Text processing pipeline (normalization, translation integration with Gemini)
Phase 4: Frontend migration (port existing Angular components to React, keep Three.js logic intact)
Phase 5: Asset & storage migration (Firebase Storage → AWS S3 or local file system)
Phase 6: Authentication (Unkey → Spring Security + JWT)
Phase 7: Testing & deployment

3. Tech Stack Decisions

List all recommended Java/Spring dependencies (Maven or Gradle)
List all React libraries to keep or replace
Database choice if needed (PostgreSQL, MongoDB, etc.)

4. API Mapping Table
   Create a table mapping old Firebase endpoints to new Spring Boot endpoints:
   | Old Endpoint (Firebase) | New Endpoint (Spring Boot) | Method | Notes |
5. Risk & Mitigation

Identify top risks (e.g. sign language pose data format compatibility, Three.js integration, Gemini latency)
Provide mitigation strategy for each

6. Definition of Done

Clear checklist for each phase to confirm completion

Format everything in clean Markdown with checkboxes - [ ] for all actionable items. Save to the project root and confirm the file path when done.# Kiến trúc sign.mt

## Kiến trúc cốt lõi: Quy trình dịch thuật

Quy trình dịch thuật của sign.mt tuân theo một quy trình xử lý đa giai đoạn từ văn bản đầu vào đến đầu ra hình ảnh ngôn ngữ ký hiệu:

**Các giai đoạn của quy trình:**

1. **Đầu vào văn bản**: Người dùng nhập văn bản qua giao diện người dùng React
2. **Chuẩn hóa văn bản**: Tiền xử lý để sửa lỗi chính tả, chữ hoa/chữ thường và định dạng
3. **Dịch văn bản sang văn bản**: Dịch giữa các ngôn ngữ nói
4. **Tạo ngôn ngữ ký hiệu**: Chuyển đổi văn bản đã dịch thành hoạt ảnh ngôn ngữ ký hiệu
5. **Hiển thị hoạt ảnh**: Hiển thị avatar 3D với biểu cảm khuôn mặt và cử chỉ tay

Kiến trúc này tách biệt các mối quan tâm giữa frontend (React) và backend (Firebase Functions), với các ranh giới API rõ ràng.

## Dòng dữ liệu: Ánh xạ từ glosses sang ngôn ngữ ký hiệu

Việc ánh xạ từ văn bản sang ngôn ngữ ký hiệu liên quan đến nhiều thành phần liên kết:

**Các tệp và mô-đun chính:**

- **Frontend (React)**:
  - `sign-writing.service.ts`: Dịch vụ cốt lõi ánh xạ văn bản sang các thành phần ngôn ngữ ký hiệu
  - `hands.service.ts`: Quản lý đại diện cử chỉ tay
  - `body.service.ts`: Xử lý tư thế và chuyển động cơ thể
  - `face.service.ts`: Kiểm soát biểu cảm khuôn mặt và chuyển động mắt
  - `translate.service.ts`: Điều phối quy trình dịch thuật
  - `translate.component.ts`: Thành phần giao diện người dùng chính điều phối quá trình

- **Backend (Firebase Functions)**:
  - `text-normalization/controller.ts`: Chuẩn hóa văn bản đầu vào (chính tả, chữ hoa/chữ thường, đơn vị)
  - `text-to-text/controller.ts`: Dịch giữa các ngôn ngữ nói bằng mô hình mạng thần kinh
  - `text-to-text/model/model.ts`: Triển khai mô hình dịch thuật với Gemini
  - `gateway/spoken-to-signed.ts`: Điểm cuối chính chuyển đổi văn bản thành hoạt ảnh ngôn ngữ ký hiệu

**Quy trình dòng dữ liệu:**

1. Người dùng nhập văn bản trong frontend React
2. Văn bản được gửi đến `text-normalization/controller.ts` để tiền xử lý
3. Văn bản đã chuẩn hóa được chuyển đến `text-to-text/controller.ts` để dịch ngôn ngữ
4. Văn bản đã dịch được gửi đến `gateway/spoken-to-signed.ts`, thực hiện:
   - Gọi mô hình dịch thuật (`text-to-text/model/model.ts`)
   - Tạo dữ liệu tư thế ngôn ngữ ký hiệu dựa trên văn bản đã dịch
   - Trả về dữ liệu tư thế cho frontend
5. Frontend sử dụng `sign-writing.service.ts` để ánh xạ dữ liệu tư thế sang:
   - Cử chỉ tay (`hands.service.ts`)
   - Tư thế cơ thể (`body.service.ts`)
   - Biểu cảm khuôn mặt (`face.service.ts`)
6. Thành phần hoạt ảnh hiển thị avatar 3D sử dụng các mô hình GLB từ `/assets/models/`

## API/Điểm cuối

Hệ thống sử dụng nhiều API chính:

| Phương thức | Điểm cuối | Đầu vào | Đầu ra |
|------------|-----------|---------|--------|
| POST | `/gateway/spoken-to-signed` | `{text: string, languageCode: string}` | JSON với dữ liệu tư thế cho hoạt ảnh |
| POST | `/gateway/text-normalization` | `{text: string, languageCode: string}` | Chuỗi văn bản đã chuẩn hóa |
| POST | `/gateway/text-to-text` | `{text: string, sourceLanguage: string, targetLanguage: string}` | Chuỗi văn bản đã dịch |
| GET | `/gateway/avatars` | Không có | Danh sách avatar có sẵn |
| POST | `/gateway/avatars/:avatarId` | Dữ liệu hình ảnh | Avatar mới được tạo |

**API bên ngoài:**

- **Gemini**: Được sử dụng cho việc chuẩn hóa văn bản và dịch thuật (qua `text-normalization/model.ts` và `text-to-text/model/model.ts`)
- **Firebase Storage**: Lưu trữ hình ảnh và hoạt ảnh avatar
- **Google Cloud Storage**: Quản lý tài sản avatar
- **Unkey Authentication**: Middleware xác thực API key

## Quản lý tài sản: Hoạt ảnh ngôn ngữ ký hiệu

Dự án quản lý hoạt ảnh ngôn ngữ ký hiệu thông qua một hệ thống tài sản tinh vi:

**Cấu trúc tài sản:**

- **Mô hình 3D**: Lưu trữ trong `/src/assets/models/`
  - `pose-animation/model.json`: Dữ liệu tư thế hoạt ảnh chính
  - `hand-shape/model.json`: Mô hình cử chỉ tay
  - `face-features/model.json`: Mô hình biểu cảm khuôn mặt
  - `sign-detector/model.json`: Mô hình phát hiện ngôn ngữ ký hiệu
- **Tài sản hoạt ảnh**:
  - `.glb` files: Định dạng mô hình 3D cho avatar
  - `.json` files: Dữ liệu tư thế hoạt ảnh
  - `.png` files: Văn bản ngoại hình avatar
  - `.mp4` files: Video giới thiệu và nội dung quảng bá

**Quy trình hoạt ảnh:**

1. Điểm cuối `spoken-to-signed` tạo dữ liệu tư thế (vị trí khớp, xoay)
2. Dữ liệu này được ánh xạ đến mô hình avatar 3D sử dụng các tệp GLB
3. `sign-writing.service.ts` trên frontend áp dụng:
   - Hình dạng tay từ `hand-shape/model.json`
   - Tư thế cơ thể từ `pose-animation/model.json`
   - Biểu cảm khuôn mặt từ `face-features/model.json`
4. Three.js hiển thị hoạt ảnh cuối cùng trong trình duyệt

**Giao tiếp tài sản:**

- Tài sản tĩnh được phục vụ trực tiếp từ thư mục công khai của frontend
- Hình ảnh avatar được lưu trữ trong Firebase Storage và truy cập thông qua URL được ký
- Tệp mô hình được đóng gói với ứng dụng frontend (không tải động)
- Hệ thống sử dụng một bộ mô hình đã được huấn luyện cố định thay vì tạo hoạt ảnh trên máy chủ

## Chiến lược di chuyển cho Spring Boot + React

**Di chuyển Backend (Spring Boot):**

1. Thay thế Firebase Functions bằng các trình điều khiển REST của Spring Boot
2. Triển khai các điểm cuối tương đương:
   - `/api/normalize` (chuẩn hóa văn bản)
   - `/api/translate` (dịch ngôn ngữ)
   - `/api/spoken-to-signed` (tạo ngôn ngữ ký hiệu)
3. Sử dụng các thư viện NLP dựa trên Java (Stanford NLP, Apache OpenNLP) để chuẩn hóa văn bản
4. Triển khai dịch thuật bằng các thư viện mạng thần kinh dựa trên Java (DL4J, Deeplearning4j)
5. Lưu trữ tài sản avatar trong hệ thống tệp hoặc lưu trữ đám mây (AWS S3, Azure Blob Storage)
6. Sử dụng Spring Security để xác thực API thay vì Unkey

**Di chuyển Frontend (React):**

1. Giữ nguyên cấu trúc thành phần React hiện tại (sign-writing.service.ts, hands.service.ts, v.v.)
2. Thay thế các lời gọi Firebase Storage bằng các lời gọi đến API Spring Boot của bạn
3. Sử dụng cùng các thư viện hoạt ảnh 3D (Three.js) với cùng các mô hình GLB
4. Giữ nguyên cùng cấu trúc tài sản trong `/src/assets/models/`
5. Giữ nguyên cùng định dạng dữ liệu tư thế để tương thích

**Các cân nhắc quan trọng:**

- Hệ thống hoạt ảnh ngôn ngữ ký hiệu chủ yếu dựa trên frontend với xử lý backend tối thiểu
- Backend chủ yếu xử lý xử lý văn bản, không tạo hoạt ảnh
- Các mô hình 3D và logic hoạt ảnh có thể được di chuyển trực tiếp với ít thay đổi
- Tập trung vào việc triển khai quy trình xử lý văn bản bằng Java trước
- Hệ thống hiển thị hoạt ảnh đã được thiết kế tốt và có thể được sử dụng trực tiếp
