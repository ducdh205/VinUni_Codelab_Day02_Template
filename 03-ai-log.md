# 03 — AI Log: Nhật ký tương tác AI (Lab 02 — Vin Smart Future)

> ⚠️ **Lưu ý trước khi nộp:** Đây là bản nháp dựng dựa trên chính quá trình trao đổi thực tế của bạn với AI trong buổi lab. Hãy đọc kỹ, sửa lại theo đúng trải nghiệm cá nhân, cảm nhận, và ví dụ của bạn — đừng nộp nguyên văn. Phần chấm điểm ưu tiên tính **trung thực**, không phải văn phong hoa mỹ.

---

## 1. AI đã giúp gì?

Trong buổi lab, mình dùng AI (Claude) như một **thought-partner** xuyên suốt cả 3 giai đoạn: Scan, Quick-Assess, và Deep-Dive.

- **Ở Phase 1 (Scan):** Khi chưa có đủ ý tưởng, mình dùng đúng prompt gợi ý trong worksheet để brainstorm 5 pain point thực tế cho mảng Xanh SM. AI đưa ra nhanh 5 bài toán bám khá sát 4 lenses (lặp lại, tốn thời gian, AI-upgrade, stakeholder pain), kèm số liệu ước tính tổn thất — giúp mình tiết kiệm thời gian so với việc tự nghĩ từ đầu.
- **Ở Phase 2 (Quick-Assess):** AI giúp dựng nhanh 3 Quick Problem Cards theo đúng khung mẫu (Actor, workflow 4-5 bước, bottleneck, metric, architecture), giúp mình có bản nháp có cấu trúc để chỉnh sửa thay vì phải tự canh format thủ công.
- **Ở Phase 3 (Deep-Dive):** AI giúp mở rộng 1 card thành báo cáo đầy đủ — vẽ current-state workflow dạng box diagram, điền Problem Statement 6-field, thiết kế future-state flow có AI step/HITL/fallback, và viết phần Evaluate với quyết định GO/NOT YET/NO-GO kèm lý giải.

Điểm mạnh nhất: AI rất giỏi giữ **cấu trúc nhất quán** giữa các phase (workflow hiện tại → bottleneck → problem statement → future flow đều khớp với nhau), và luôn nhắc mình nghĩ đến **Operational Boundary** — phần mình dễ bỏ sót nếu tự làm một mình.

---

## 2. AI trả lời sai / hallucination ở đâu?

Đây là phần quan trọng nhất cần trung thực:

- **Số liệu là ước tính, không phải dữ liệu thật:** Các con số như "10-12% cuốc bị ảnh hưởng", "80 lượt sự cố pin/ngày", "20 giờ công/ngày lãng phí" đều do AI **tự ước lượng có vẻ hợp lý** để minh hoạ, không dựa trên số liệu thật của Xanh SM. Nếu nộp bài mà không kiểm chứng, đây chính là dạng hallucination "nghe hợp lý nhưng không có nguồn" — nhóm cần thay bằng số liệu thật (nếu có) hoặc ghi rõ đây là giả định cần xác thực.
- **Tự chọn bài toán mà không hỏi ý kiến nhóm:** Khi mình yêu cầu làm Deep-Dive, AI tự chọn Card #2 (điểm đón sai) thay vì hỏi nhóm thực sự muốn Deep-Dive bài nào. Đây không hẳn là "sai" nhưng là một giả định AI tự đưa ra — nhóm thật cần họp và tự quyết định, không nên để AI chọn thay.
- **Quyết định "NOT YET" mang tính minh hoạ:** Ở phần Evaluate, AI chọn quyết định NOT YET và tự viết lý giải dựa trên checklist do chính AI tự đánh giá (ví dụ mục "stakeholders sẵn sàng thay đổi" — AI tự cho là chưa đạt). Đây là **judgment giả định**, không phản ánh khảo sát thực tế với tài xế hoặc đội vận hành Xanh SM. Nhóm cần tự đánh giá lại 3 mục checklist dựa trên hiểu biết thật của nhóm, không copy nguyên văn phần lý giải của AI.
- **Operational Boundary có thể chưa đủ chặt:** AI đề xuất ngưỡng "bán kính an toàn 150m" cho việc điều chỉnh điểm đón — đây là con số minh hoạ tự đưa ra, chưa được kiểm chứng với thực tế vận hành (ví dụ 150m có thể quá xa ở khu đô thị đông đúc, hoặc quá gần ở khu ngoại thành).

---

## 3. Mình đã sửa prompt / ranh giới ra sao để đạt kết quả chuẩn?

- Yêu cầu AI bám sát đúng cấu trúc file mẫu (`02-deliverable-example.md`) thay vì để AI tự do sáng tạo định dạng khác — giúp output nhất quán với rubric chấm điểm.
- Sau khi nhận bản nháp Deep-Dive, mình sẽ **đối chiếu lại số liệu ước tính với dữ liệu vận hành thật của Xanh SM** (nếu nhóm tiếp cận được) hoặc ghi chú rõ "*ước tính, cần xác thực*" thay vì trình bày như số liệu chính thức.
- Với phần chọn bài toán Deep-Dive và quyết định GO/NOT YET/NO-GO, mình sẽ **họp lại với nhóm** để tự quyết định thay vì giữ nguyên lựa chọn của AI — vì đây là phần thể hiện tư duy phản biện của chính nhóm, không nên để AI quyết định thay.
- Với Operational Boundary, mình sẽ hỏi lại AI dạng phản biện (theo đúng tip trong worksheet): *"Hãy đóng vai CFO/Trưởng vận hành khắt khe, chỉ ra 3 điểm yếu về logic và ranh giới của giải pháp này"* — để stress-test lại trước khi chốt bản cuối, thay vì chấp nhận ngay bản nháp đầu tiên.

---

## 4. Bài học rút ra

AI là công cụ tốt để **tăng tốc phần cấu trúc và trình bày**, nhưng không thể thay thế cho **kiến thức thực địa** (số liệu thật, ý kiến stakeholder thật) và **quyết định mang tính trách nhiệm** (chọn bài toán nào, GO hay NOT YET). Ranh giới rõ ràng nhất mình rút ra: dùng AI để **soạn thảo nhanh và giữ format chuẩn**, nhưng mọi con số và quyết định cuối cùng phải được nhóm tự kiểm chứng và chịu trách nhiệm.
