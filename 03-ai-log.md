# Nhật ký tương tác AI (AI Log) — Lab 02

**1. AI đã hỗ trợ tôi những gì?**
*   Lên ý tưởng và cấu trúc các bài toán vận hành (Scan & Quick Cards) cho Xanh SM, VinFast, Vinmec.
*   Thiết kế luồng hoạt động (Future-State Flow) và thiết lập ranh giới an toàn (Operational Boundaries) cho bài toán phân tích cuốc xe hủy của Xanh SM.
*   Viết mã Python và các kịch bản kiểm thử đối kháng (Adversarial Tests) để kết nối Gemini API.

**2. AI đã sai hoặc bị ảo giác (Hallucination) ở đâu?**
*   **Sử dụng thư viện cũ:** Ban đầu AI cung cấp code dùng thư viện `google.generativeai` đã bị Google thông báo ngừng hỗ trợ (deprecated).
*   **Vi phạm ranh giới an toàn:** Khi chạy kiểm thử Test Case 1 (đưa vào dữ liệu lan man: *"trời mưa to, đợi mãi không thấy ai đón..."*), AI đã phớt lờ quy tắc. Thay vì trả về kết quả `"CẦN_NGƯỜI_DUYỆT"`, mô hình tự ý suy diễn và bịa ra lý do `"Tài xế không đến đón (Khách hàng chờ lâu)"`.

**3. Bài học và cách khắc phục**
*   **Về code:** Cần chủ động cập nhật thư viện mới (`google.genai`) theo tài liệu chính thức thay vì hoàn toàn tin tưởng code AI sinh ra.
*   **Về Prompting:** Việc dùng từ khóa "TUYỆT ĐỐI KHÔNG" là chưa đủ để ngăn mô hình LLM suy diễn. Trong tương lai, cần bổ sung thêm Few-shot prompting (đưa ra các ví dụ mẫu cụ thể về việc từ chối trả lời) hoặc thêm một Rule-based filter để chặn các output không nằm trong danh sách 10 lý do mặc định.