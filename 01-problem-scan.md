# 01 — Problem Scan & Quick Assessment

> **Phạm vi:** Hoàn thành Phase 0–2 trong file này; Phase 3–5 nằm trong `02-deep-dive-report.md`. Deep-dive đã chọn: **Xanh SM — Phân tích lý do hủy chuyến**.
>
> **Nguyên tắc dữ liệu:** Các mục đánh dấu **⚠️ Cần dữ liệu thực tế** phải được xác minh bằng log vận hành, phỏng vấn stakeholder hoặc báo cáo nội bộ trước khi dùng làm baseline/cam kết kinh doanh.

---

## Phase 0 — Đọc và rút kinh nghiệm từ Worked Example

Đã đọc worked example **Xanh SM Intelligent Dispatcher** trong `02-deliverable-example.md`. Những nguyên tắc được áp dụng cho bài làm này:

1. **Problem First, AI Second:** bắt đầu từ quy trình vận hành và điểm nghẽn, không chọn công nghệ AI trước khi hiểu bài toán.
2. **Actor và workflow phải cụ thể:** xác định rõ ai thực hiện, input/output của từng bước, handoff và bước gây tắc nghẽn.
3. **Metric phải đo được:** tách baseline hiện tại, mục tiêu kỳ vọng và nguồn dữ liệu dùng để xác minh.
4. **Chọn AI Fit vừa đủ:** so sánh No AI/Rule/LLM/Agent; không mặc định Agent là phương án tốt nhất.
5. **Operational Boundary:** AI chỉ hỗ trợ trong phạm vi cho phép; các quyết định ảnh hưởng trực tiếp đến tài xế hoặc khách hàng cần Human-in-the-loop và fallback.
6. **Không coi số liệu minh họa là dữ liệu thật:** các con số trong worked example chỉ dùng để học cấu trúc, không được tái sử dụng như số liệu thực tế của Xanh SM.

---

## Phase 1 — SCAN: Tìm kiếm cơ hội

Phạm vi ưu tiên là **Mảng 1 — Ô tô & Di chuyển xanh (VinFast và Xanh SM)**, đặc biệt là ý tưởng số 1 và số 4 trong Inspiration Kit.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | **Xanh SM** | Tốn thời gian | **Điều vận thông minh:** điều phối viên phải đọc tin nhắn/mô tả điểm đón của tài xế, đối chiếu với GPS và liên lạc lại khi vị trí thực tế không khớp điểm đón trên ứng dụng. |
| 2 | **Xanh SM** | Pain từ người khác | **Phân tích lý do hủy chuyến:** nguyên nhân hủy nằm rải rác trong ghi âm tổng đài, phản hồi khách hàng và ghi chú tự do của tài xế, khiến nhóm vận hành khó xác định các nguyên nhân chính. |
| 3 | **VinFast** | Lặp lại | **Đối chiếu hóa đơn sạc điện đối tác:** nhân viên định kỳ so khớp phiên sạc, biểu giá, dữ liệu trụ sạc và hóa đơn từ nhiều nguồn để phát hiện chênh lệch. |
| 4 | **VinFast** | AI có thể tốt hơn | **Phân loại mô tả lỗi xe bằng tiếng Việt:** khách hàng thường mô tả tiếng động hoặc hiện tượng xe bằng ngôn ngữ tự nhiên; nhân viên phải diễn giải và chuyển thông tin đến đúng nhóm kỹ thuật. |
| 5 | **VinFast** | Tốn thời gian | **Hỗ trợ tìm trạm sạc phù hợp:** nhân viên hỗ trợ phải kiểm tra vị trí, loại xe/cổng sạc, tình trạng trạm và mức pin trước khi đề xuất phương án cho người lái. |
| 6 | **Xanh SM** | Lặp lại | **Tổng hợp sự cố chuyến xe theo ca:** trưởng ca phải gom dữ liệu từ ticket, cuộc gọi và ghi chú điều phối để lập báo cáo bàn giao và nhận diện sự cố lặp lại. |

### Dữ liệu thực tế cần bổ sung cho Phase 1

Để xếp hạng sáu cơ hội bằng tác động thật thay vì cảm tính, cần các dữ liệu sau:

- Số case/ngày hoặc số case/ca của từng quy trình.
- Thời gian xử lý trung bình và phân vị P90/P95 cho một case.
- Tỷ lệ chuyển xử lý, mở lại ticket, sai phân loại hoặc cần liên hệ lại.
- Chi phí nhân sự cho mỗi case hoặc tổng giờ công mỗi tháng.
- Ảnh hưởng đến SLA, tỷ lệ hoàn thành/hủy chuyến và trải nghiệm khách hàng.
- Hệ thống/dữ liệu hiện có và quyền truy cập dữ liệu của nhóm triển khai.

> **⚠️ Cần dữ liệu thực tế:** Chưa có log hoặc báo cáo nội bộ trong repository để xác nhận các baseline trên.

---

## Phase 2 — QUICK-ASSESS

Ba bài toán được chọn để đánh giá nhanh là **#1 Điều vận thông minh**, **#2 Phân tích lý do hủy chuyến** và **#3 Đối chiếu hóa đơn sạc điện**. Hai bài toán đầu là ưu tiên theo lựa chọn ban đầu; bài toán thứ ba được dùng làm phương án đối chứng vì có cấu trúc rõ và có thể phù hợp với rule-based hơn LLM.

### Quick Problem Card #1 — Xanh SM: Điều vận thông minh

| Trường | Nội dung |
|---|---|
| **Bài toán** | Khi mô tả điểm đón, ghim bản đồ và GPS thực tế không khớp nhau, điều phối viên phải xử lý thủ công để xác nhận điểm đón khả thi và hướng dẫn tài xế. |
| **Công ty thành viên** | **Xanh SM (GSM)** |
| **Ai đang đau (Actor)** | Chính: điều phối viên. Liên quan: tài xế đang chờ xác nhận và khách hàng đang chờ xe. |
| **Workflow hiện tại (5 bước)** | 1. Hệ thống/điều phối nhận case bất thường → 2. Điều phối viên đọc mô tả của khách/tài xế → 3. Đối chiếu ghim điểm đón với GPS và bản đồ → 4. Gọi/nhắn để làm rõ nếu dữ liệu mâu thuẫn → 5. Xác nhận điểm đón hoặc chuyển phương án cho tài xế. |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–4: hiểu mô tả tự do, đối chiếu nhiều nguồn và trao đổi lại. **⚠️ Cần dữ liệu thực tế:** thời gian trung bình, P95 và tỷ lệ phải liên hệ lại. |
| **AI có thể hỗ trợ** | Trích xuất địa danh/mốc nhận biết từ tin nhắn, phát hiện mâu thuẫn với GPS, tóm tắt tình huống và soạn **phương án nháp** cho điều phối viên. Tính khoảng cách/geofence và kiểm tra vùng cấm nên dùng rule/GIS, không giao cho LLM tự suy đoán. |
| **Success metric đề xuất** | (1) Ít nhất **80%** case đủ dữ liệu được tạo bản tóm tắt/gợi ý trong **≤10 giây**; (2) giảm **≥50%** thời gian xử lý trung vị so với baseline; (3) **0%** chỉ dẫn được gửi tự động mà chưa có điều phối viên duyệt; (4) tỷ lệ gợi ý được điều phối viên chấp nhận **≥85%** trong pilot. |
| **Quick Architecture** | ☐ No AI · ☑ Rule + LLM Feature · ☐ Agent tự trị |
| **Operational Boundary sơ bộ** | AI không tự thay đổi điểm đón, không tự gửi chỉ dẫn và không quyết định thay con người khi dữ liệu GPS/mô tả mâu thuẫn. Nếu thiếu dữ liệu hoặc độ tin cậy thấp, chuyển điều phối viên xử lý thủ công. |

#### Dữ liệu cần lấy cho Card #1

- Log thời gian từ lúc case phát sinh đến lúc xác nhận điểm đón.
- Tỷ lệ case có ghim/GPS/mô tả mâu thuẫn và tỷ lệ phải gọi lại.
- Mẫu tin nhắn đã ẩn danh và kết quả xử lý cuối cùng.
- Tỷ lệ hủy hoặc thời gian đón tăng thêm do xử lý chậm.
- Quy định hiện hành về vùng đón, điểm cấm dừng và quyền quyết định của điều phối viên.

---

### Quick Problem Card #2 — Xanh SM: Phân tích lý do hủy chuyến

| Trường | Nội dung |
|---|---|
| **Bài toán** | Nguyên nhân hủy chuyến nằm trong nhiều nguồn dữ liệu phi cấu trúc nên nhóm vận hành khó phân loại nhất quán và phát hiện nguyên nhân gốc. |
| **Công ty thành viên** | **Xanh SM (GSM)** |
| **Ai đang đau (Actor)** | Chuyên viên phân tích vận hành, QA/tổng đài và quản lý đội xe; gián tiếp là tài xế và khách hàng chịu tác động từ các nguyên nhân chưa được khắc phục. |
| **Workflow hiện tại (5 bước)** | 1. Trích xuất danh sách chuyến hủy → 2. Tìm ghi âm, transcript và ghi chú liên quan → 3. Nghe/đọc từng case → 4. Gán lý do theo nhóm → 5. Tổng hợp bảng biểu và gửi báo cáo cho quản lý. |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–4: liên kết nhiều nguồn, đọc/nghe thủ công và gán nhãn không đồng nhất. **⚠️ Cần dữ liệu thực tế:** số chuyến hủy cần review, phút/case và mức đồng thuận giữa người gán nhãn. |
| **AI có thể hỗ trợ** | Chuyển giọng nói thành văn bản (nếu chưa có transcript), tóm tắt, phân loại đa nhãn theo taxonomy đã duyệt, trích dẫn đoạn bằng chứng và gom các case chưa tự tin để con người review. |
| **Success metric đề xuất** | (1) Phân loại tự động **≥80%** case có đủ dữ liệu; (2) macro-F1 **≥0,85** trên tập kiểm thử được con người gán nhãn; (3) giảm **≥60%** giờ review thủ công; (4) **100%** case độ tin cậy thấp được đưa vào hàng chờ review thay vì tự chốt. |
| **Quick Architecture** | ☐ No AI · ☑ LLM Feature + pipeline xử lý dữ liệu · ☐ Agent tự trị |
| **Operational Boundary sơ bộ** | AI chỉ tạo nhãn phân tích nội bộ; không tự quy trách nhiệm cho tài xế/khách hàng, không áp dụng phạt/thưởng và không liên hệ khách hàng. Dữ liệu thoại/PII phải được phân quyền, ẩn danh và tuân thủ thời hạn lưu trữ. |

#### Dữ liệu cần lấy cho Card #2

- Tổng số và tỷ lệ chuyến hủy theo ngày/tuần, theo khu vực và khung giờ.
- Taxonomy lý do hủy hiện tại và định nghĩa của từng nhãn.
- Mẫu transcript/ghi chú đã ẩn danh cùng nhãn chuẩn do chuyên gia xác nhận.
- Thời gian review thủ công và tỷ lệ bất đồng nhãn giữa các reviewer.
- Tỷ lệ bản ghi thiếu, lỗi âm thanh hoặc không liên kết được với chuyến.
- Chính sách truy cập, ẩn danh và lưu trữ dữ liệu ghi âm/PII.

---

### Quick Problem Card #3 — VinFast: Đối chiếu hóa đơn sạc điện đối tác

| Trường | Nội dung |
|---|---|
| **Bài toán** | Nhân viên phải so khớp dữ liệu phiên sạc với biểu giá và hóa đơn đối tác để phát hiện thiếu phiên, trùng giao dịch hoặc sai số tiền. |
| **Công ty thành viên** | **VinFast** |
| **Ai đang đau (Actor)** | Nhân viên tài chính/đối soát và đầu mối vận hành trạm sạc đối tác. |
| **Workflow hiện tại (5 bước)** | 1. Nhận file phiên sạc và hóa đơn → 2. Chuẩn hóa mã trạm, thời gian, đơn giá → 3. Ghép giao dịch giữa các nguồn → 4. Kiểm tra chênh lệch → 5. Lập danh sách ngoại lệ và trao đổi với đối tác. |
| **Bước tốn thời gian/lỗi nhất** | Bước 2–4 khi định dạng/mã định danh không đồng nhất hoặc thiếu dữ liệu. **⚠️ Cần dữ liệu thực tế:** số dòng mỗi kỳ, tỷ lệ ghép thất bại và số giờ đối soát. |
| **AI có thể hỗ trợ** | Phần ghép và tính chênh lệch chủ yếu dùng ETL + rule. LLM chỉ nên hỗ trợ ánh xạ tên cột/định dạng lạ và soạn giải thích cho ngoại lệ, sau đó con người duyệt. |
| **Success metric đề xuất** | (1) Đối chiếu tự động **≥95%** giao dịch hợp lệ; (2) giảm **≥70%** thời gian xử lý mỗi kỳ; (3) độ chính xác phát hiện chênh lệch **≥99,5%** trên bộ dữ liệu đã xác minh; (4) **100%** ngoại lệ tài chính được người có thẩm quyền duyệt. |
| **Quick Architecture** | ☐ No AI · ☑ Rule/ETL · ☐ LLM là lõi · ☐ Agent |
| **Operational Boundary sơ bộ** | Hệ thống không tự phê duyệt thanh toán, sửa hóa đơn gốc hoặc kết luận trách nhiệm đối tác. Các ngưỡng chênh lệch và biểu giá phải đến từ cấu hình được phê duyệt. |

#### Dữ liệu cần lấy cho Card #3

- File mẫu của từng nguồn và data dictionary.
- Số giao dịch/kỳ, tỷ lệ thiếu/trùng và tỷ lệ ghép tự động hiện tại.
- Biểu giá, quy tắc làm tròn, thuế/phí và ngưỡng chênh lệch được chấp nhận.
- Tổng giờ công mỗi kỳ và số ngoại lệ phải trao đổi với đối tác.
- Bộ giao dịch đã đối soát đúng để làm ground truth.

---

## Đánh giá sơ bộ sau Phase 2

| Tiêu chí | Điều vận thông minh | Phân tích lý do hủy | Đối chiếu hóa đơn sạc |
|---|---|---|---|
| Tác động gần thời gian thực | Cao | Thấp–Trung bình | Thấp |
| Mức phù hợp với dữ liệu ngôn ngữ | Cao | Cao | Thấp–Trung bình |
| Mức phù hợp với rule-based | Trung bình (rule + GIS cần thiết) | Trung bình | Cao |
| Rủi ro khi đầu ra sai | Cao | Trung bình | Cao |
| Human-in-the-loop cần thiết | Bắt buộc | Bắt buộc với case không chắc chắn | Bắt buộc trước quyết định tài chính |
| Dữ liệu cần xác minh trước Phase 3 | Log điều vận, GPS, tin nhắn | Transcript, taxonomy, nhãn chuẩn | File giao dịch, biểu giá, ground truth |

## Kết quả lựa chọn

- **Bài toán được chọn cho Deep-Dive:** **Xanh SM — Phân tích lý do hủy chuyến** (item 4, Inspiration Kit).
- **Lý do lựa chọn:** (1) dữ liệu phi cấu trúc phong phú (ghi âm tổng đài, transcript, ghi chú tự do của tài xế) là bài toán LLM tự nhiên; (2) rủi ro khi đầu ra sai thấp hơn điều vận thời gian thực — kết quả sai chỉ ảnh hưởng báo cáo nội bộ, không trực tiếp tác động tài xế/khách; (3) đo lường được bằng macro-F1 trên bộ nhãn chuẩn.
- **Hai ưu tiên ban đầu của người học:** Điều vận thông minh (item 1) và Phân tích lý do hủy chuyến (item 4).
- **Lý do chưa chọn Điều vận thông minh (item 1):** tác động gần thời gian thực nên rủi ro khi gợi ý sai cao; cần kết hợp thêm rule/GIS, phức tạp hơn khi chỉ đánh giá nhanh giá trị của một LLM feature.
- **Bài toán đối chứng (Đối chiếu hóa đơn sạc):** có khả năng được giải quyết tốt hơn bằng ETL/rule-based; không nên dùng LLM làm lõi nếu cấu trúc dữ liệu và quy tắc đã xác định.

---

## Danh sách dữ liệu cần người học/nhóm cung cấp

Để chuyển sang Phase 3 mà không bịa số liệu, vui lòng thu thập hoặc xác nhận tối thiểu:

1. **Cho Điều vận thông minh:** số case/ngày, thời gian xử lý trung vị và P95, tỷ lệ phải gọi lại, tỷ lệ hủy liên quan, 20–50 case đã ẩn danh.
2. **Cho Phân tích hủy chuyến:** số chuyến hủy/ngày, taxonomy lý do, thời gian review/case, 50–100 transcript/ghi chú đã ẩn danh và nhãn chuẩn.
3. **Nguồn dữ liệu:** mô tả hệ thống lưu log, trường dữ liệu có sẵn và quyền sử dụng dữ liệu cho bài lab.
4. **Stakeholder:** xác nhận ai là người vận hành, ai phê duyệt đầu ra và ai chịu trách nhiệm khi hệ thống gợi ý sai.

Nếu không thể tiếp cận dữ liệu nội bộ, Phase 3 cần ghi rõ đây là **pilot scoping dựa trên giả định**, đồng thời trình bày kế hoạch đo baseline trước khi quyết định GO.
