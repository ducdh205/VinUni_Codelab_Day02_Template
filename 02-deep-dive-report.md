# 02 — Deep-Dive Report: Xanh SM Smart Pickup-Point Suggestion

> **Bài toán chọn Deep-Dive:** Card #2 — Hệ thống gợi ý điểm đón dựa trên bản đồ tĩnh khiến tài xế mất nhiều thời gian tìm khách thực tế.
> **Công ty thành viên:** Xanh SM (GSM)

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ App tính     │     │ Tài xế nhận  │     │ Tài xế đến   │     │ Không thấy   │
│ điểm đón cố  │ ──→ │ cuốc, di     │ ──→ │ điểm đón trên│ ──→ │ khách tại    │
│ định (bản đồ │     │ chuyển đến   │     │ bản đồ       │     │ điểm gợi ý   │
│ tĩnh)        │     │ điểm         │     │              │     │ 🔴           │
│ Ai: Hệ thống │     │ Ai: Tài xế   │     │ Ai: Tài xế   │     │ Ai: Tài xế   │
│ ⏱ tức thì    │     │ ⏱ 3-8 phút   │     │ ⏱ 1 phút     │     │ ⏱ 0 phút     │
│ In: Toạ độ   │     │ In: Điểm đón │     │ In: GPS thực │     │ In: Quan sát │
│ khách đặt xe │     │ trên bản đồ  │     │ tế           │     │ thực địa     │
│ Out: Điểm    │     │ Out: Vị trí  │     │ Out: Trạng   │     │ Out: "Không  │
│ đón gợi ý    │     │ xe cập nhật  │     │ thái đến nơi │     │ tìm thấy"    │
└──────────────┘     └──────────────┘     └──────────────┘     └──────┬───────┘
                                                                       ▼
                                                                ┌──────────────┐
                                                                │ Bước 5       │
                                                                │ Tài xế tự    │
                                                                │ gọi/nhắn tin │
                                                                │ hỏi khách    │
                                                                │ vị trí chính │
                                                                │ xác 🔄🔴     │
                                                                │ Ai: Tài xế + │
                                                                │ Khách hàng   │
                                                                │ ⏱ 4-6 phút   │
                                                                │ In: SĐT khách│
                                                                │ Out: Chỉ dẫn │
                                                                │ bằng lời nói │
                                                                └──────┬───────┘
                                                                       ▼
                                                                ┌──────────────┐
                                                                │ Bước 6       │
                                                                │ Tài xế di    │
                                                                │ chuyển lại   │
                                                                │ đến vị trí   │
                                                                │ chính xác    │
                                                                │ Ai: Tài xế   │
                                                                │ ⏱ 1-2 phút   │
                                                                └──────────────┘

🔴 Bottleneck: Bước 4 (điểm gợi ý sai) và Bước 5 (phải liên hệ thủ công)
🔄 Handoff: Bước 5 — chuyển giao thông tin giữa Tài xế và Khách hàng qua thoại
⏱ Tổng thời gian xử lý trung bình: ~10-15 phút/cuốc bị ảnh hưởng
   (so với ~2-3 phút nếu điểm đón chính xác ngay từ đầu)
```

**Ghi chú:** Vấn đề xảy ra chủ yếu ở các khu vực có địa hình phức tạp — chung cư nhiều cổng, khu phố có rào chắn/công trình thi công, hoặc khu vực bản đồ chưa cập nhật thay đổi hạ tầng gần đây.

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Tài xế Xanh SM đang thực hiện đón khách theo điểm hệ thống gợi ý; gián tiếp liên quan là khách hàng (người chờ) và tổng đài CSKH (tiếp nhận khiếu nại khi phát sinh). |
| **2. Current Workflow** | Hệ thống tính điểm đón dựa trên toạ độ GPS tĩnh mà khách nhập khi đặt xe, không đối chiếu với dữ liệu thực địa (cổng ra vào toà nhà, rào chắn công trình, khu vực cấm dừng đỗ). Khi tài xế không tìm thấy khách tại điểm gợi ý, phải tự gọi điện/nhắn tin hỏi khách vị trí chính xác — hoàn toàn thủ công, không có công cụ hỗ trợ. |
| **3. Bottleneck** | Bước 4-5: điểm đón sai + phải liên hệ thoại thủ công để xác nhận lại vị trí. Đây là bước duy nhất phụ thuộc hoàn toàn vào giao tiếp ngôn ngữ tự nhiên giữa hai bên, không có dữ liệu hỗ trợ trung gian. |
| **4. Business Impact** | Ước tính 10-12% cuốc xe/ngày bị ảnh hưởng bởi điểm đón không chính xác. Mỗi cuốc bị ảnh hưởng mất thêm trung bình 5-8 phút xử lý, làm giảm số cuốc hoàn thành/ca của tài xế (ảnh hưởng trực tiếp thu nhập), tăng tỉ lệ khách hủy chuyến do chờ lâu, và tăng khối lượng khiếu nại gửi về tổng đài CSKH. |
| **5. Success Metric** | 1. Giảm thời gian tìm khách trung bình từ 5 phút xuống dưới 2 phút (Efficiency).<br>2. Giảm tỉ lệ cuốc bị ảnh hưởng bởi điểm đón sai từ ~10-12% xuống dưới 4% (Quality).<br>3. Giảm số cuộc gọi thoại phát sinh giữa tài xế-khách hàng để xác nhận vị trí xuống dưới 3% tổng số cuốc (Automation rate). |
| **6. Operational Boundary** | AI được phép: phân tích ghi chú/tin nhắn khách nhập khi đặt xe (ví dụ "cổng sau toà nhà", "cạnh quán cà phê") kết hợp dữ liệu GPS lịch sử của các điểm đón thành công trước đó tại cùng khu vực để tinh chỉnh điểm đón gợi ý. **CẤM:** AI không được tự động điều hướng tài xế đến địa điểm nằm ngoài bán kính an toàn (>150m so với toạ độ gốc khách cung cấp) mà không có xác nhận; không được thay thế hoàn toàn liên lạc trực tiếp khi hệ thống không đủ tin cậy (confidence thấp) — trong trường hợp đó phải fallback về gợi ý tài xế chủ động gọi khách như quy trình cũ. |

---

## 3.3. Future-State Flow & AI Fit

**AI Fit Matrix:** [x] LLM Feature — không cần Agentic Loop vì bài toán có phạm vi rõ ràng (tinh chỉnh 1 toạ độ điểm đón), không cần chuỗi hành động đa bước tự trị; Rule-based đơn thuần không đủ vì cần xử lý ngôn ngữ tự nhiên (ghi chú khách hàng) và học từ dữ liệu lịch sử.

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Khách đặt xe,│     │ 🔵 AI phân   │     │ 🔵 AI tính   │     │ 🟢 App hiển  │
│ nhập ghi chú │ ──→ │ tích ghi chú │ ──→ │ điểm đón tinh│ ──→ │ thị điểm đón │
│ vị trí (nếu  │     │ + đối chiếu  │     │ chỉnh + độ   │     │ + độ tin cậy;│
│ có)          │     │ dữ liệu điểm │     │ tin cậy      │     │ tài xế xác   │
│              │     │ đón lịch sử  │     │ (confidence) │     │ nhận trước   │
│              │     │ thành công   │     │              │     │ khi di chuyển│
└──────────────┘     └──────────────┘     └──────┬───────┘     └──────┬───────┘
                                                  │                    │
                                    confidence thấp                   ▼
                                                  │             ┌──────────────┐
                                                  ▼             │ Bước 5       │
                                           ┌──────────────┐     │ Tài xế đến   │
                                           │ ↩️ Fallback: │     │ điểm đón,    │
                                           │ Giữ nguyên   │     │ tìm khách    │
                                           │ điểm đón gốc │     │ thành công   │
                                           │ + gợi ý tài  │     └──────────────┘
                                           │ xế chủ động  │
                                           │ gọi khách    │
                                           │ (như quy     │
                                           │ trình cũ)    │
                                           └──────────────┘
```

- 🔵 **AI Step:** Bước 2-3 — phân tích ngôn ngữ tự nhiên trong ghi chú khách + đối chiếu dữ liệu điểm đón lịch sử để đề xuất toạ độ chính xác hơn, kèm điểm tin cậy (confidence score).
- 🟢 **Human Step (HITL):** Bước 4 — tài xế luôn là người xác nhận cuối cùng trước khi di chuyển; app hiển thị rõ đây là gợi ý AI (không phải lệnh bắt buộc).
- ↩️ **Fallback:** Khi AI không đủ tin cậy (confidence thấp, ví dụ ghi chú không rõ ràng hoặc không có dữ liệu lịch sử khu vực đó), hệ thống giữ nguyên điểm đón gốc theo GPS tĩnh và quay về quy trình cũ (tài xế tự liên hệ khách qua thoại).

---

# 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? — Có dữ liệu lịch sử điểm đón thành công/thất bại từ hệ thống vận hành hiện tại của Xanh SM, có thể dùng để huấn luyện/kiểm chứng mô hình.
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? — Có. AI chỉ đưa ra gợi ý, tài xế luôn xác nhận trước khi di chuyển; giới hạn bán kính 150m và cơ chế fallback về quy trình cũ khi confidence thấp giúp giới hạn thiệt hại tối đa nếu AI sai.
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? — Cần khảo sát thêm ý kiến tài xế (nhóm dùng trực tiếp) và đội vận hành khu vực trước khi triển khai diện rộng; hiện tại mới xác nhận được sự ủng hộ từ đội sản phẩm, chưa có dữ liệu khảo sát tài xế thực tế.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> Bài toán có tiềm năng ROI rõ ràng (10-12% cuốc bị ảnh hưởng, tác động trực tiếp đến thu nhập tài xế và tỉ lệ hủy chuyến), kiến trúc kỹ thuật đơn giản (LLM Feature với fallback an toàn), và rủi ro được kiểm soát tốt qua HITL + giới hạn bán kính di chuyển. Tuy nhiên, hai điều kiện chưa đủ để "GO" ngay: (1) chưa có khảo sát trực tiếp từ tài xế — nhóm dùng chính của tính năng — để xác nhận họ tin tưởng và sẵn sàng làm theo gợi ý AI thay vì thói quen cũ; (2) chưa xác lập baseline định lượng chính xác (hiện tại các con số 10-12% và 5-8 phút là ước tính, cần đo đạc thực tế qua log hệ thống trong 2-4 tuần trước khi commit nguồn lực phát triển). Đề xuất: chạy khảo sát nhanh với 20-30 tài xế tại khu vực có bottleneck cao nhất (chung cư/khu công trình), đồng thời trích xuất log thực tế để xác nhận baseline, sau đó review lại quyết định GO trong 3-4 tuần tới.