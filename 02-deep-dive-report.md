# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
**Vẽ quy trình hiện tại lên bảng/giấy A3.** Sử dụng các ký hiệu:
* 🔴 **Bottleneck:** Bước gây tắc nghẽn, tốn thời gian, hoặc sai sót nhiều nhất.
* 🔄 **Handoff:** Điểm chuyển giao thông tin giữa người và hệ thống, hoặc giữa các bộ phận.
* Ghi rõ thời gian vận hành trung bình: **Tổng cộng = ____ phút/lượt**.

Quy trình xử lý một cuộc gọi kỹ thuật tại tổng đài CSKH VinFast hiện nay:

```text
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ Bước 1       │   │ Bước 2       │   │ Bước 3       │   │ Bước 4       │
│ Khách gọi    │   │ NV ghi chú   │   │ Tra sổ mã lỗi│   │ Định tuyến   │
│ mô tả lỗi    │──→│ vào CRM      │──→│ và đoán nhóm  │──→│ bộ phận KT / │
│              │   │              │   │ lỗi 🔴       │   │ đặt lịch 🔄  │
│ Ai: Khách+NV │   │ Ai: NV tổng  │   │ Ai: NV tổng  │   │ Ai: NV tổng  │
│ ⏱ 2 phút     │   │ đài ⏱ 1 phút │   │ đài ⏱ 4 phút │   │ đài ⏱ 2 phút │
│ In: Lời nói  │   │ In: Ghi chú  │   │ In: Sổ mã lỗi│   │ Out: Ticket  │
└──────────────┘   └──────────────┘   └──────────────┘   └──────┬───────┘
                                                                 │
                                                (Nếu đoán sai)────┘
                                                                 ▼
                                                         ┌──────────────┐
                                                         │ Rework 🔴    │
                                                         │ Khách bị     │
                                                         │ chuyển máy   │
                                                         │ lại từ đầu   │
                                                         └──────────────┘

🔴 = Bottleneck   🔄 = Handoff (chuyển giao người ↔ hệ thống)
⏱ Tổng thời gian: ~9 phút/cuộc (chưa tính rework khi định tuyến sai)
```

**Nhận xét:** Bottleneck nằm ở **Bước 3** — nhân viên phải dựa vào kinh nghiệm cá nhân và sổ tay mã lỗi để đoán nhóm lỗi từ mô tả mơ hồ của khách (*"xe kêu cụp cụp", "màn hình bị đơ", "sạc không vào"*). Định tuyến sai ở Bước 4 tạo ra vòng lặp rework tốn thêm thời gian và làm khách khó chịu.

## 3.2. Problem Statement (6-field) & Metrics (15 min)
Điền đầy đủ 6 trường thông tin của bài toán:

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Ai đang thực hiện tác vụ hằng ngày? |
| **2. Current Workflow** | Mô tả tóm tắt quy trình thủ công hiện tại và công cụ sử dụng. |
| **3. Bottleneck** | Bước nào chậm, lỗi, hoặc cần xử lý ngôn ngữ tự động nhiều nhất? |
| **4. Business Impact** | Tổn thất thực tế đo bằng thời gian, chi phí, hoặc SLA của Vingroup. |
| **5. Success Metric** | AI giải quyết được thì đạt ngưỡng số mấy? (Ví dụ: *"85% vé được phân loại dưới 10s"*). |
| **6. Operational Boundary** | AI được phép làm gì, TUYỆT ĐỐI không được làm gì, điểm nào cần duyệt? |

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên tổng đài CSKH VinFast (hotline kỹ thuật). Người chịu ảnh hưởng gián tiếp: khách hàng phải chờ và bị chuyển máy. |
| **2. Current Workflow** | Khách gọi mô tả triệu chứng bằng lời → NV ghi chú vào CRM → tra sổ tay mã lỗi (Excel/wiki nội bộ) và dựa vào kinh nghiệm để đoán nhóm lỗi → tạo ticket và định tuyến đến bộ phận kỹ thuật phù hợp hoặc đặt lịch xưởng. 4 bước, thủ công, ~9 phút/cuộc. |
| **3. Bottleneck** | **Bước 3** — Tra cứu & đoán nhóm lỗi từ ngôn ngữ tự do tiếng Việt. Phụ thuộc kinh nghiệm cá nhân, không chuẩn hóa; nhân viên mới thường đoán sai. |
| **4. Business Impact** | Ước tính ~**500 cuộc gọi kỹ thuật/ngày** toàn quốc. Tỉ lệ định tuyến sai lần đầu ~**15–20%** → mỗi ca sai tốn thêm ~5 phút rework + giảm điểm hài lòng (CSAT). Tổng lãng phí ~**40 giờ nhân sự/ngày** và tăng thời gian chờ của khách. *(Số liệu minh họa — nhóm thay bằng số thực nếu có.)* |
| **5. Success Metric** | **(1) Efficiency:** Giảm thời gian phân loại từ 4 phút → dưới 1 phút/cuộc.<br>**(2) Quality:** Tỉ lệ định tuyến đúng bộ phận ngay lần đầu ≥ 90% (giảm rework xuống < 8%).<br>**(3) Adoption:** ≥ 80% gợi ý của AI được nhân viên chấp nhận mà không cần sửa. |
| **6. Operational Boundary** | **AI ĐƯỢC phép:** đọc mô tả triệu chứng, gợi ý top 3 nhóm lỗi kèm độ tin cậy và bộ phận đề xuất, tạo bản nháp (draft) ticket.<br>**AI TUYỆT ĐỐI KHÔNG:** tự động chốt ticket hay chuyển máy khách khi chưa có NV xác nhận; không đưa ra chẩn đoán an toàn/kỹ thuật khẳng định chắc chắn (VD "phanh của bạn an toàn"); không hứa hẹn chi phí, thời gian sửa, hay điều kiện bảo hành.<br>**Điểm cần duyệt (HITL):** Nhân viên tổng đài phải xác nhận nhóm lỗi & bộ phận trước khi định tuyến. |


## 3.3. Future-State Flow & AI Fit (25 min)
* **Xác định mức AI Fit (AI-Fit Matrix):** Giải pháp thuộc nhóm nào? [ ] Rule / State-Machine [ ] LLM Feature [ ] Agentic Loop.
* **Vẽ Future-State Flow:** Đánh dấu rõ:
  * 🔵 **AI Step:** Tác vụ LLM xử lý.
  * 🟢 **Human Step (HITL):** Bước con người phê duyệt/review (Human-in-the-loop).
  * ↩️ **Fallback:** Kế hoạch dự phòng khi LLM trả về kết quả lỗi hoặc không tự tin.

### 🎯 AI-Fit Matrix

| Lựa chọn | Phù hợp? | Lý do |
|---|:---:|---|
| Rule / State-Machine | ❌ | Mô tả của khách là ngôn ngữ tự do, biến thể gần như vô hạn; hệ luật từ khóa cứng sẽ bỏ sót và bảo trì rất tốn kém. |
| **LLM Feature** | ✅ | Đúng thế mạnh: hiểu văn bản tiếng Việt tự do → phân loại vào tập nhóm lỗi hữu hạn. Một lần gọi model là đủ. |
| Agentic Loop | ❌ | Không cần chuỗi nhiều bước tự quyết hay gọi nhiều công cụ; thêm agent chỉ làm hệ thống phức tạp và khó kiểm soát ranh giới. |

> **Kết luận AI-Fit:** ✅ **LLM Feature** — bài toán phân loại văn bản một bước, có tập nhãn xác định, kèm người duyệt.

### 🔄 Future-State Flow (có tích hợp AI)

```text
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│ Bước 1       │   │ Bước 2 🔵    │   │ Bước 3 🟢    │   │ Bước 4       │
│ Khách mô tả  │   │ LLM phân     │   │ NV xem gợi ý │   │ Định tuyến   │
│ lỗi (thoại   │──→│ loại → JSON: │──→│ xác nhận /   │──→│ đúng bộ phận │
│ hoặc chat)   │   │ top3 nhóm    │   │ sửa (HITL)   │   │ + tạo ticket │
│              │   │ lỗi + độ tin │   │              │   │              │
│ ⏱ 2 phút     │   │ cậy ⏱ ~5s    │   │ ⏱ ~30 giây   │   │ ⏱ tự động    │
└──────────────┘   └──────┬───────┘   └──────────────┘   └──────────────┘
                          │
        (độ tin cậy < 0.6 HOẶC lỗi API) ──↩️ Fallback──┐
                                                        ▼
                                                 ┌──────────────┐
                                                 │ Quy trình cũ │
                                                 │ NV xử lý thủ │
                                                 │ công / hỏi   │
                                                 │ khách làm rõ │
                                                 └──────────────┘

🔵 AI Step   🟢 Human-in-the-loop   ↩️ Fallback
⏱ Tổng thời gian: ~2.5 phút/cuộc (giảm từ ~9 phút)
```

### 🧩 Cơ chế an toàn

- **🟢 Human-in-the-loop:** Model chỉ **gợi ý** dưới dạng draft; nhân viên tổng đài là người bấm xác nhận/sửa trước khi định tuyến. AI không bao giờ tự chốt.
- **↩️ Fallback:** Nếu độ tin cậy của model dưới ngưỡng (VD `< 0.6`), hoặc API lỗi/timeout, hệ thống **tự động quay về quy trình thủ công cũ** và có thể hiển thị câu hỏi làm rõ cho khách. Không bao giờ để hệ thống "đoán bừa" khi không chắc.


---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [ ] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [ ] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[ ] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> *Viết lý giải chi tiết tại đây*

## AI Readiness Checklist

| # | Câu hỏi | Trả lời | Giải thích |
|---|---|:---:|---|
| 1 | Có sẵn dữ liệu mẫu/logs sạch để test? | ✅ Có | Lịch sử ticket CRM đã có sẵn cặp *(mô tả của khách → nhóm lỗi thực tế)* để xây tập test và đo độ chính xác. |
| 2 | Rủi ro khi AI sai có nằm trong tầm kiểm soát? | ✅ Có | Mọi gợi ý đều qua nhân viên duyệt (HITL) và có Fallback; AI sai chỉ dẫn tới việc NV sửa lại, không gây hậu quả trực tiếp cho khách. |
| 3 | Stakeholders sẵn sàng đổi quy trình? | ⏳ Một phần | Cần đào tạo nhân viên tổng đài tin tưởng và biết cách sửa gợi ý; nên triển khai thí điểm ở 1 nhóm nhỏ trước. |

## 🗳️ Quyết định của Ban Giám Đốc Vin Smart Future

> ### ✅ **GO — Bắt đầu xây dựng Prototype với scope hẹp**

**Justification (lý giải dựa trên bằng chứng):**

1. **Đúng AI-Fit:** Đây là bài toán phân loại văn bản tiếng Việt tự do — thế mạnh rõ ràng của LLM, không thể thay bằng rule-based hiệu quả.
2. **Rủi ro thấp, kiểm soát tốt:** Có Human-in-the-loop bắt buộc và Fallback về quy trình cũ; AI không có quyền tự chốt hay chuyển máy khách. Ranh giới cấm rõ ràng (không khẳng định an toàn, không hứa chi phí/bảo hành).
3. **Giá trị đo được:** Giảm thời gian phân loại ~4 phút → dưới 1 phút và giảm rework từ ~18% xuống < 8% là mục tiêu cụ thể, đo được ngay trên dữ liệu CRM hiện có.
4. **Scope hẹp để bắt đầu:** Thí điểm với top 10–15 nhóm lỗi phổ biến nhất tại 1 nhóm tổng đài, đo baseline vs. sau AI trong 2–4 tuần rồi mới mở rộng.

---