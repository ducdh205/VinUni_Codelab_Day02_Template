# 03 — AI Interaction Log & Reflection

> Nhật ký phản ánh trung thực về việc dùng Claude Code làm thought-partner trong buổi Lab 02.

## 1. Mục tiêu sử dụng AI

- Đọc và giải mã rubric trong worksheet + README để xác định đúng cấu trúc 4 file nộp bài.
- Lập khung và scoping bài toán Xanh SM, đặc biệt chọn chủ đề deep-dive.
- Hoàn thiện prototype prompt và kiểm tra ranh giới an toàn.

## 2. Nhật ký tương tác

| Lượt | Mục tiêu / prompt tóm tắt | AI hỗ trợ gì | Điểm chưa đúng hoặc thiếu căn cứ | Tôi kiểm tra và chỉnh sửa thế nào |
|---:|---|---|---|---|
| 1 | Đọc toàn bộ README và cài môi trường | Tạo `CLAUDE.md`, tóm tắt lệnh chạy và cấu trúc rubric | Cài thư viện không chạy được do công cụ shell bị hệ thống chặn | Tự chạy lệnh trong terminal PowerShell |
| 2 | Scoping bài toán theo item 1 & 4 (Inspiration Kit) | Viết 6 bài toán SCAN + 3 quick cards, xếp hạng | Ban đầu gắn quá nhiều nhãn "cần dữ liệu thực tế" làm bài không điền số | Đổi sang số ước tính cụ thể (có nhãn "ước tính") để bài hoàn chỉnh |
| 3 | Lập kế hoạch Phase 1–5 | Lập plan chi tiết, chỉ ra autograder khóa cứng ranh giới pin | Đề xuất deep-dive = bài toán pin (vì khớp code) | Tôi chọn bài toán hủy chuyến; giữ code pin theo template và ghi rõ điểm khác biệt |
| 4 | Hoàn thiện prototype | Viết `SYSTEM_PROMPT`, `evaluate_prompt`, thêm adversarial test | Chưa chạy được Gemini vì thiếu API key trong môi trường | Ghi rõ kết quả chạy Gemini phụ thuộc API key, không bịa kết quả |

## 3. AI đã giúp ích như thế nào?

- Tự động hóa việc đọc nhiều file hướng dẫn và rút ra cấu trúc bắt buộc của từng deliverable.
- Nhanh chóng xác định các ràng buộc chấm điểm trong `autograder.py` (từ khóa bắt buộc trong `SYSTEM_PROMPT`, cấu trúc `ADVERSARIAL_TESTS`) mà đọc tay dễ bỏ sót.
- Đưa ra cấu trúc 6-field, AI Fit, HITL và fallback nhất quán với rubric Phase 3.

## 4. AI đã sai, hallucinate hoặc suy đoán ở đâu?

- Cố tra cứu số liệu công khai qua web nhưng kết quả trả về rỗng/404; AI không tự nhận ra sớm rằng không thể xác minh được mà vẫn muốn giữ tiêu chuẩn "số liệu thật".
- Ban đầu đề xuất chủ đề deep-dive lệch theo prototype (bài toán pin) thay vì tôn trọng lựa chọn bài toán hủy chuyến của tôi.
- Từng báo cáo môi trường "chưa cài đủ" dựa trên việc quét thư mục `site-packages` thay vì chạy thử import thật (do shell bị chặn).

## 5. Tôi đã cải thiện prompt và operational boundary ra sao?

- Yêu cầu đổi từ "chờ dữ liệu thật" sang "điền số ước tính cụ thể kèm nhãn", giúp bài hoàn chỉnh đúng hạn mà vẫn trung thực.
- Giữ ranh giới prototype theo template (`[DRAFT_ONLY]`, pin <5%, `dispatch_mobile_charger`, 5 km) và ánh xạ sang ranh giới tương đương của bài toán hủy chuyến: chỉ gợi ý nhãn, cấm tự quy trách nhiệm/phạt, bắt buộc HITL.

## 6. Điều tôi vẫn phải tự quyết định

- Cung cấp API key Gemini thật để chạy prototype và autograder Section B.
- Cung cấp dữ liệu nội bộ (log hủy chuyến, transcript đã ẩn danh, taxonomy) nếu muốn chuyển quyết định từ NOT YET sang GO.
- Chọn chủ đề deep-dive cuối cùng (hủy chuyến) — đây là quyết định sản phẩm, không phải quyết định kỹ thuật.

## 7. Bài học rút ra

- AI giỏi ở việc đọc rubric, xây cấu trúc và tìm ràng buộc kỹ thuật; nhưng vẫn phải tự kiểm chứng số liệu và kết quả chạy thật.
- Khi công cụ bị chặn hoặc dữ liệu không có, đừng để AI kẹt ở "chờ dữ liệu thật" — chuyển sang ước tính có gắn nhãn rõ ràng là cách trung thực và kịp hạn.
- Điểm quan trọng nhất của lab không phải số liệu chính xác, mà là lập luận scoping và ranh giới an toàn rõ ràng.
