# Báo Cáo Phân Tích Sâu (Deep-Dive Report) — Vin Smart Future

## 🏛️ Bối cảnh
Tôi là Trần Gia Khánh, AI Engineer tại **Vin Smart Future**. Nhóm chúng tôi phối hợp với Khối Vận Hành **Xanh SM** để giải quyết bài toán: quá tải trong việc nghe và phân tích nguyên nhân khách hàng hủy chuyến hằng ngày.

---

## 🏗️ Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow (Quy trình hiện tại)
*(Xem sơ đồ chi tiết tại file `04-workflow-diagram.png` đính kèm)*
*   **Bước 1:** Trích xuất file danh sách cuốc hủy (1 phút).
*   **Bước 2:** Nghe ghi âm và đọc ghi chú thủ công 🔴 **[Bottleneck]** (5 phút/ca).
*   **Bước 3:** Nhập lý do vào Excel 🔄 **[Handoff]** (1 phút).
*   **Bước 4:** Tổng hợp báo cáo.
*   **Tổng thời gian:** ~7 phút/ca, hoàn toàn thủ công.

### 3.2. Problem Statement (6-field) & Metrics
| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên phân tích dữ liệu (Data Analyst) tại Xanh SM. |
| **2. Current Workflow** | Nghe ghi âm/đọc text thủ công từng ca hủy chuyến và tự gõ lý do vào file quản lý. |
| **3. Bottleneck** | Việc xử lý ngôn ngữ tự nhiên không cấu trúc (nghe/đọc) tốn quá nhiều thời gian. |
| **4. Business Impact** | Chỉ phân tích được 10-15% tổng cuốc hủy. Bỏ lọt các nguyên nhân hệ thống hoặc lỗi tài xế, gây rò rỉ doanh thu. |
| **5. Success Metric** | AI tự động phân loại 100% cuốc hủy. Giảm thời gian xử lý xuống dưới 10 giây/ca. Độ chính xác > 90%. |
| **6. Operational Boundary** | **ĐƯỢC PHÉP:** Gán 1 trong 10 nhãn lý do có sẵn. <br>**CẤM:** Không được tự bịa lý do mới. Nếu độ tin cậy thấp, bắt buộc trả về tag `[CẦN_NGƯỜI_DUYỆT]`. |

### 3.3. Future-State Flow & AI Fit
*   **AI Fit:** [x] LLM Feature (Vì chỉ xử lý văn bản một chiều, không cần AI Agent tự trị).
*   **Quy trình tương lai (Future-State):**
    *   1. Kéo dữ liệu cuốc hủy ──> 2. 🔵 **AI Step:** Tự động đọc và phân loại nhãn ──> 3. 🟢 **HITL:** Chuyên viên chỉ duyệt các ca bị gắn tag `[CẦN_NGƯỜI_DUYỆT]` ──> 4. Xuất báo cáo.
*   **Fallback ↩️:** Nếu hệ thống AI lỗi, dữ liệu tự xuất ra file Excel để chuyên viên đọc thủ công như cũ.

---

## 🏁 Phase 5 — EVALUATE

**AI Readiness Checklist:**
*   [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? *(Có sẵn log text và ghi âm)*
*   [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát? *(Có, đây là tác vụ back-office, không chat trực tiếp với khách, sai có thể sửa tay)*
*   [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

**Quyết định cuối cùng:**
*   [x] **GO (Bắt đầu xây dựng Prototype)**

**Justification (Lý giải):**
Bài toán an toàn, có metric đo lường rõ ràng. LLM Feature sẽ xử lý được 100% dữ liệu thay vì 10%, giúp Xanh SM ngay lập tức phát hiện các lỗ hổng vận hành với chi phí API cực kỳ thấp so với thiệt hại kinh doanh.
```[cite: 2, 3, 6, 7]

Sau khi lưu file này, bước tiếp theo của bạn là chuyển sang code file Python nhé. Bạn có câu hỏi nào trước khi sang phần code không?