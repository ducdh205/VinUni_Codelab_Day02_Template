# 02 — Problem Deep-Dive Report

> **Phạm vi:** Phase 3 (Deep-Dive) + Phase 5 (Evaluate).
>
> **Nguyên tắc số liệu:** Các con số vận hành nội bộ đều là **ước tính phục vụ scoping** và cần xác minh bằng log/báo cáo nội bộ trước khi triển khai thật. Không có dữ liệu nội bộ trong repository.

## 1. Bài toán được lựa chọn

- **Tên bài toán:** Phân tích lý do hủy chuyến của Xanh SM (Cancellation Reason Analysis).
- **Công ty thành viên:** **Xanh SM (GSM)** — Vận hành taxi/xe máy điện thông minh.
- **Phạm vi phân tích:** Hỗ trợ nhân viên vận hành phân loại nguyên nhân hủy chuyến từ các nguồn phi cấu trúc (ghi âm tổng đài, transcript, ghi chú tự do của tài xế), thay cho việc nghe/đọc thủ công từng case.

## 2. Current-State Workflow Mapping

Quy trình phân loại lý do hủy chuyến hiện tại (thủ công):

| Bước | Actor / hệ thống | Hoạt động | Input | Output | Thời gian | Handoff / Bottleneck |
|---:|---|---|---|---|---:|---|
| 1 | Hệ thống GSM | Ghi nhận chuyến bị hủy (khách hủy / tài xế hủy) | Sự kiện hủy từ app | Log chuyến hủy | ~0 phút | — |
| 2 | Chuyên viên phân tích | Xuất danh sách chuyến hủy theo ngày/khu vực | Log chuyến hủy | File danh sách (CSV) | ~2 phút/case | 🔄 Handoff hệ thống → người |
| 3 | Chuyên viên phân tích | Mở ghi âm cuộc gọi, transcript, ghi chú tài xế của từng case | File danh sách + dữ liệu thoại | Tập thông tin thô mỗi case | ~3 phút/case | — |
| 4 | Chuyên viên phân tích | Nghe/đọc từng case và gán nhãn lý do hủy theo danh mục | Thông tin thô | Nhãn lý do hủy | ~7 phút/case | 🔴 Bottleneck |
| 5 | Chuyên viên phân tích | Tổng hợp bảng biểu, gửi báo cáo cho quản lý vận hành | Nhãn đã gán | Báo cáo nguyên nhân | ~1 phút/case | 🔄 Handoff người → quản lý |

- **Tổng thời gian hiện tại:** ≈ **13 phút/case** (ước tính).
- **Bottleneck chính:** Bước 3–4 — nghe/đọc thủ công từng bản ghi và gán nhãn. Thời gian tăng mạnh khi ghi âm dài, nhiều case thiếu transcript hoặc nhãn không đồng nhất giữa các chuyên viên.
- **Nguồn số liệu / giả định cần kiểm chứng:** 13 phút/case và phân bổ từng bước là ước tính; cần log thời gian thao tác thực tế và phỏng vấn nhân viên QA để xác minh.

## 3. Problem Statement — 6 Fields

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên phân tích vận hành (QA/operation) thuộc Khối Vận hành Xanh SM; gián tiếp là quản lý đội xe và bộ phận tổng đài. |
| **2. Current Workflow** | Khi có chuyến bị hủy, hệ thống ghi nhận vào log. Chuyên viên xuất danh sách hủy, mở ghi âm/transcript/ghi chú của từng case, nghe/đọc và gán nhãn lý do, rồi tổng hợp báo cáo gửi quản lý. 5 bước thủ công, ≈13 phút/case (ước tính). |
| **3. Bottleneck** | Bước 3–4 (≈10 phút/case): liên kết nhiều nguồn dữ liệu phi cấu trúc, nghe/đọc thủ công, và gán nhãn không đồng nhất giữa các chuyên viên (ước tính mức đồng thuận chỉ ~70%). |
| **4. Business Impact** | Với đội xe quy mô lớn (GSM công bố khoảng 30.000 ô tô điện và 100.000 xe máy điện — theo công bố công khai, cần xác minh), ước tính ~1.200 chuyến hủy/tuần cần phân loại → khoảng **260 giờ công/tuần** cho việc review thủ công. Chậm phát hiện nguyên nhân gốc khiến tỷ lệ hủy lặp lại và rò rỉ doanh thu kéo dài. *(ước tính — cần xác minh)* |
| **5. Success Metric** | (1) Phân loại tự động đạt **macro-F1 ≥ 0.85** trên tập nhãn chuẩn do chuyên gia xác nhận; (2) giảm **≥ 60%** giờ review thủ công; (3) **100%** case có độ tin cậy thấp được đưa vào hàng chờ con người review thay vì tự chốt nhãn. |
| **6. Operational Boundary** | AI được phép: chuyển giọng nói thành văn bản, gợi ý đa nhãn theo taxonomy đã duyệt, và trích dẫn đoạn bằng chứng. **CẤM:** tự quy trách nhiệm cho tài xế/khách, tự áp dụng phạt/thưởng, tự liên hệ khách hàng. Dữ liệu thoại/PII phải được phân quyền, ẩn danh và tuân thủ thời hạn lưu trữ. |

## 4. AI Fit

| Phương án | Mức phù hợp | Lợi ích | Hạn chế / rủi ro |
|---|---|---|---|
| Rule / State Machine | Thấp | Đơn giản, dễ kiểm soát | Không xử lý được ngôn ngữ tự do; từ khóa đơn thuần bỏ sót ngữ cảnh và không trích được bằng chứng |
| **LLM Feature** | **Cao** | Xử lý ghi âm/transcript tiếng Việt phi cấu trúc, gợi ý nhãn + trích dẫn bằng chứng | Cần taxonomy chuẩn + bộ nhãn chuẩn để đánh giá; cần HITL với case không chắc chắn |
| Agentic Loop | Trung bình | Tự động hóa nhiều bước (lấy dữ liệu → gán nhãn → báo cáo) | Tăng rủi ro tự quyết; không cần thiết cho bài toán phân tích nội bộ có cấu trúc |

- **Phương án được chọn:** **LLM Feature** (có HITL).
- **Lý do:** Dữ liệu chủ yếu là ngôn ngữ tự nhiên phi cấu trúc nên rule-based không đủ; nhưng quy trình có cấu trúc cố định và đầu ra sai có thể quy trách nhiệm sai cho con người, nên không cần Agent tự trị.

## 5. Future-State Flow

| Bước | Loại bước | Hoạt động | Điều kiện / ranh giới |
|---:|---|---|---|
| 1 | Hệ thống | Ghi nhận chuyến hủy, gom ghi âm/transcript/ghi chú | Tự động, chỉ với case có dữ liệu |
| 2 | 🔵 AI Step | Chuyển giọng → text (nếu chưa có transcript), gợi ý đa nhãn + trích dẫn đoạn bằng chứng | Dùng taxonomy đã duyệt; không tự quy kết trách nhiệm |
| 3 | 🟢 Human Step (HITL) | Chuyên viên duyệt/điều chỉnh nhãn trước khi vào báo cáo chính thức | Bắt buộc duyệt trước khi xuất báo cáo |
| 4 | Hệ thống | Tổng hợp bảng biểu, gửi báo cáo quản lý | Chỉ sau khi chuyên viên duyệt |

### Human-in-the-loop

Chuyên viên phân tích là người duyệt cuối cùng mọi nhãn. AI chỉ đưa gợi ý kèm đoạn bằng chứng để người duyệt đối chiếu nhanh, không thay thế quyết định của con người đối với các case liên quan quy kết trách nhiệm.

### Fallback

Nếu độ tin cậy của AI thấp (thiếu transcript, âm thanh lỗi, nhiều nhãn xung đột), case được chuyển vào hàng chờ con người gán nhãn thủ công như quy trình cũ. Hệ thống không tự chốt nhãn trong trường hợp này.

## 6. AI Readiness Checklist

| Tiêu chí | Có | Chưa | Bằng chứng / việc cần làm |
|---|:---:|:---:|---|
| Có dữ liệu mẫu/log sạch để test | ☐ | ☑ | Chưa có tập transcript/ghi chú đã ẩn danh và nhãn chuẩn |
| Rủi ro AI sai được kiểm soát bằng HITL hoặc fallback | ☐ | ☑ | Có thiết kế HITL + fallback, nhưng chưa thử nghiệm trên dữ liệu thật |
| Stakeholders sẵn sàng thay đổi workflow | ☐ | ☑ | Chưa xác nhận với đội vận hành/QA |

## 7. Quyết định

- ☐ **GO**
- ☑ **NOT YET**
- ☐ **NO-GO**

### Justification

Chọn **NOT YET** vì bài toán khả thi về mặt kỹ thuật (LLM Feature + HITL) nhưng chưa đủ điều kiện để GO:
1. **Chưa có taxonomy chuẩn và bộ nhãn chuẩn** — không thể đo macro-F1 hay lập baseline nếu chưa có ground truth.
2. **Dữ liệu thoại chưa sẵn sàng** — chưa xác minh chất lượng transcript, tỷ lệ bản ghi thiếu, và chính sách ẩn danh PII.
3. **Rủi ro đầu ra sai chưa được đo** — dù có HITL, cần đánh giá trên dữ liệu thật trước khi cam kết giảm giờ công.

Hành động để đạt GO: thu thập 50–100 transcript/ghi chú đã ẩn danh kèm nhãn chuẩn, xác lập taxonomy, đo baseline thời gian review, rồi chạy pilot nhỏ có HITL.

## 8. Giả định và kế hoạch xác minh

| Giả định | Vì sao cần xác minh | Cách xác minh |
|---|---|---|
| ~1.200 chuyến hủy/tuần cần phân loại | Là cơ sở tính giờ công và tác động | Đối chiếu log hủy chuyến của nền tảng GSM |
| ≈13 phút/case cho review thủ công | Quyết định mức giảm giờ công kỳ vọng | Bấm giờ thao tác thực tế của chuyên viên QA |
| Mức đồng thuận nhãn ~70% | Chứng minh nhãn thủ công không nhất quán | So nhãn của 2–3 reviewer trên cùng tập case |
| macro-F1 ≥ 0.85 khả thi | Là ngưỡng thành công | Đánh giá trên tập nhãn chuẩn sau khi có ground truth |
