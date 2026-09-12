# 01 — Problem Scan (Vin Smart Future / Xanh SM)

---

# 🔍 Phase 1 — SCAN

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên tra cứu thủ công vị trí xe + trạm sạc trống khi tài xế báo sự cố hết pin giữa đường (mất 15 phút/lượt, ước tính ~80 lượt/ngày tại Hà Nội, gây lãng phí ~20 giờ công/ngày). |
| 2 | Xanh SM | Lặp lại | Phân bổ lại cuốc xe khi khách yêu cầu đổi điểm đến hoặc lộ trình giữa chừng — điều phối viên phải tính lại thủ công giá cước và thời gian, gây trễ 3-5 phút/lượt và ước tính rò rỉ ~8% doanh thu cuốc xe điều chỉnh. |
| 3 | Xanh SM | Pain từ người khác | Tài xế phàn nàn hệ thống gợi ý điểm đón không chính xác (do dữ liệu bản đồ tĩnh, không cập nhật theo tình trạng giao thông/rào chắn thực tế), khiến tài xế mất trung bình 4-6 phút tìm khách mỗi cuốc, ước tính giảm 10-12% số cuốc hoàn thành/ca làm việc. |
| 4 | Xanh SM | AI có thể tốt hơn | Tổng hợp thủ công các phản hồi/đánh giá 1-2 sao từ khách hàng sau chuyến đi để cảnh báo sớm chất lượng tài xế — hiện mất 24-48 giờ để phát hiện pattern, dẫn đến tài xế có vấn đề vẫn tiếp tục chạy thêm hàng chục cuốc trước khi bị xử lý. |
| 5 | Xanh SM | Tốn thời gian | Nghe ghi âm cuộc gọi hủy chuyến/tổng đài để phân loại nguyên nhân (tài xế hủy vì xa, khách hủy vì chờ lâu, lỗi app...) — nhân sự CSKH nghe thủ công ~200 cuộc gọi/ngày, tốn ~15 giờ công/ngày và chỉ phân tích được mẫu nhỏ (~10-15%) tổng số cuộc gọi thực tế. |

---

# 🃏 Phase 2 — QUICK-ASSESS

Top 3 bài toán được chọn để làm Quick Problem Cards: **#1 (Sự cố pin)**, **#3 (Điểm đón không chính xác)**, **#4 (Cảnh báo sớm chất lượng tài xế)**.

## Card #1 — Xử lý sự cố pin thực địa

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Điều phối viên phải tra cứu thủ công vị   │
│ trí xe và trạm sạc trống khi tài xế báo hết pin giữa đường. │
│ Công ty thành viên: [x] Xanh SM  [ ] VinFast  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                    │
│                                                             │
│ Ai đang đau (Actor)? Điều phối viên (Dispatcher) + Tài xế    │
│ đang chờ hướng dẫn                                          │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Nhận cuộc gọi sự cố ──> 2. Tra định vị GPS xe          │
│   ──> 3. Tra cứu trạm sạc trống ──> 4. Soạn tin hướng dẫn   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 10 phút/lượt)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4 (tự động lấy  │
│ vị trí, lọc trạm sạc phù hợp loại cổng, draft tin hướng dẫn)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — Gợi ý điểm đón chính xác cho tài xế

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Hệ thống gợi ý điểm đón dựa trên bản đồ   │
│ tĩnh khiến tài xế mất nhiều thời gian tìm khách thực tế.    │
│ Công ty thành viên: [x] Xanh SM  [ ] VinFast  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                    │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM (mất thời gian, giảm    │
│ thu nhập); Khách hàng (chờ lâu, dễ hủy chuyến)              │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. App gợi ý điểm đón cố định (theo bản đồ tĩnh)          │
│   ──> 2. Tài xế di chuyển đến điểm ──> 3. Không thấy khách  │
│   ──> 4. Tài xế tự nhắn tin/gọi hỏi khách vị trí chính xác  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 4-6 phút/cuốc) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1 — phân tích tin│
│ nhắn/ghi chú khách + dữ liệu GPS thực tế để tinh chỉnh điểm │
│ đón theo thời gian thực, có tính đến rào chắn/công trình    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Giảm thời gian tìm khách trung bình từ 5 phút ──> dưới 2  │
│   phút; tăng tỉ lệ hoàn thành cuốc/ca thêm 10%              │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — Cảnh báo sớm chất lượng tài xế

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Việc tổng hợp đánh giá khách hàng để phát │
│ hiện tài xế có vấn đề diễn ra quá chậm (24-48 giờ).         │
│ Công ty thành viên: [x] Xanh SM  [ ] VinFast  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                    │
│                                                             │
│ Ai đang đau (Actor)? Bộ phận Vận hành/Quản lý chất lượng    │
│ tài xế; gián tiếp là khách hàng gặp trải nghiệm xấu lặp lại │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách để lại đánh giá 1-2 sao sau chuyến               │
│   ──> 2. Dữ liệu dồn vào hệ thống, chưa được đọc ngay       │
│   ──> 3. Nhân viên định kỳ (cuối ngày) đọc và tổng hợp thủ  │
│   công ──> 4. Báo cáo lên quản lý để xử lý tài xế           │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (độ trễ 24-48h)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3 — phân tích  │
│ ngôn ngữ tự nhiên theo thời gian thực, phát hiện pattern    │
│ (thái độ, lái ẩu, xe bẩn...) và cảnh báo ngay khi đủ ngưỡng │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Giảm thời gian phát hiện pattern từ 24-48h ──> dưới 2h;   │
│   giảm số cuốc "rủi ro" tài xế chạy thêm trước khi bị xử lý │
│   từ ~20 cuốc ──> dưới 5 cuốc                                │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```