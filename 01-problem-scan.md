# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **VinFast** | AI-upgrade | Khách mô tả triệu chứng lỗi xe bằng tiếng Việt tự do (VD: *"xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"*); tổng đài phải tự tra cứu và đoán mã lỗi để định tuyến đúng bộ phận. |
| 2 | **VinFast** | Tốn thời gian | Cố vấn dịch vụ tại xưởng đọc từng phiếu bảo hành khách viết tự do, phân loại nhóm lỗi và gán mức ưu tiên hoàn toàn thủ công. |
| 3 | **VinFast** | Lặp lại | Đối chiếu hóa đơn sạc điện hằng tuần từ hàng nghìn trụ sạc đối tác với dữ liệu log thực tế của hệ thống tài chính. |
| 4 | **VinFast** | Tốn thời gian | Nhân viên CSKH soạn tay phản hồi cho hàng trăm review/khiếu nại trên App & fanpage (giao xe trễ, lỗi phần mềm, đặt cọc...). |
| 5 | **VinFast** | Pain từ người khác | Sau các buổi lái thử, ghi chú phản hồi của khách và nhân viên rời rạc; đội sản phẩm khó tổng hợp pattern lỗi/UX để cải tiến. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #___                                     │
│                                                             │
│ Bài toán (1 câu): ________________________________________  │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? ______________________________________ │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. ___ ──> 2. ___ ──> 3. ___ ──> 4. ___                   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? ___ (⏱ ___ phút/lượt)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? _____________________ │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? ______________________ │
│   VD: "Giảm thời gian soạn phản hồi từ 10 min ──> under 2 min"│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

> [!TIP]
> **🤖 AI Prompts — Stress-Test thẻ bài toán:**
> Hãy dán nội dung thẻ bài toán của bạn vào LLM để nhận phản biện:
> *"Đây là một thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: [Dán nội dung]. Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."*

## Card #1 — Chẩn đoán sơ bộ lỗi xe từ mô tả tiếng Việt

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại mã lỗi kỹ thuật ban đầu │
│ từ mô tả triệu chứng bằng tiếng Việt của khách để định       │
│ tuyến đúng bộ phận.                                          │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên tổng đài CSKH VinFast        │
│ (hotline) + khách hàng phải chờ chuyển máy nhiều lần.       │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gọi, mô tả triệu chứng bằng lời                  │
│   → 2. NV nghe, ghi chú, tra sổ tay mã lỗi                  │
│   → 3. Đoán nhóm lỗi (điện / gầm / phần mềm...)             │
│   → 4. Chuyển bộ phận kỹ thuật hoặc đặt lịch xưởng          │
│                                                             │
│ Bước nào tốn/lỗi nhất? Bước 2-3 (⏱ ~6 phút/cuộc), dễ        │
│ định tuyến sai khiến khách bị chuyển máy lòng vòng.         │
│ AI hỗ trợ ở bước nào? Bước 2-3: LLM đọc mô tả → gợi ý       │
│ top 3 mã lỗi + bộ phận phù hợp (dạng draft cho NV duyệt).   │
│                                                             │
│ Metric (có số): Giảm thời gian phân loại 6 phút ──> dưới    │
│ 1 phút; tỉ lệ định tuyến đúng bộ phận ≥ 90%.               │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — Phân loại & định tuyến phiếu bảo hành tại xưởng

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại nhóm lỗi và gợi ý mức   │
│ ưu tiên cho phiếu bảo hành khách viết tự do, rồi chuyển     │
│ đúng kỹ thuật viên.                                          │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Cố vấn dịch vụ (Service Advisor) tại   │
│ xưởng VinFast — quá tải giờ cao điểm.                      │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Nhận phiếu mô tả sự cố (khách viết tự do)             │
│   → 2. Đọc & phân loại nhóm lỗi                            │
│   → 3. Gán mức ưu tiên / kiểm tra điều kiện bảo hành       │
│   → 4. Chuyển kỹ thuật viên phù hợp                        │
│                                                             │
│ Bước nào tốn/lỗi nhất? Bước 2-3 (⏱ ~8 phút/phiếu); tồn     │
│ đọng phiếu vào giờ cao điểm.                               │
│ AI hỗ trợ ở bước nào? Bước 2-3: LLM phân loại nhóm lỗi +   │
│ đề xuất ưu tiên (draft, cố vấn phê duyệt — HITL).          │
│                                                             │
│ Metric (có số): Giảm thời gian phân loại 8 phút ──> dưới    │
│ 2 phút; ≥ 85% phiếu được gán đúng nhóm lỗi.               │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — Đối chiếu hóa đơn sạc điện đối tác

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                             │
│ Bài toán (1 câu): So khớp dữ liệu sạc hằng tuần từ hàng     │
│ nghìn trụ sạc đối tác với hóa đơn đối tác gửi về, phát      │
│ hiện dòng chênh lệch.                                        │
│ Công ty thành viên: [x] VinFast (mạng lưới sạc)            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên kế toán / vận hành mạng     │
│ lưới sạc.                                                    │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Tải log sạc từ hệ thống trụ đối tác                   │
│   → 2. Tải hóa đơn đối tác gửi                             │
│   → 3. So khớp thủ công từng dòng (kWh, giờ, mã trạm)      │
│      trên Excel                                             │
│   → 4. Đánh dấu chênh lệch, gửi lại đối tác                │
│                                                             │
│ Bước nào tốn/lỗi nhất? Bước 3 (⏱ vài giờ/tuần), dễ sót     │
│ dòng khi khối lượng lớn.                                    │
│ AI hỗ trợ ở bước nào? Bước 3 chủ yếu là so khớp dữ liệu    │
│ có cấu trúc ──> script Rule-based làm tốt & rẻ hơn; chỉ    │
│ cần LLM cho phần ghi chú tự do (nếu có).                   │
│                                                             │
│ Metric (có số): Giảm thời gian đối chiếu ~6 giờ/tuần ──>    │
│ dưới 1 giờ; phát hiện 100% dòng chênh lệch > ngưỡng.      │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [ ] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘


---