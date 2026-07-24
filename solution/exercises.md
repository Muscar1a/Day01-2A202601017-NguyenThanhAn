# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay các dòng placeholder bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> 
Khi tăng **temperature** từ 0.0 lên 1.5, phản hồi có xu hướng đa dạng và sáng tạo hơn, thể hiện qua việc mô hình lựa chọn nhiều chủ đề khác nhau thay vì lặp lại các câu trả lời phổ biến. Tuy nhiên, trong các ví dụ này, chất lượng, độ dài, thời gian phản hồi và chi phí gần như không thay đổi đáng kể; sự khác biệt chủ yếu nằm ở mức độ ngẫu nhiên của nội dung được sinh ra.


### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> 
Đối với chatbot hỗ trợ khách hàng, mình sẽ chọn temperature khoảng 0.2–0.3. Do chatbot cần trả lời nhất quán, chính xác và ít ngẫu nhiên, tránh mỗi lần trả lời một kiểu hoặc tự sáng tạo thông tin không có trong tài liệu. Mức temperature thấp vẫn giúp câu trả lời tự nhiên, nhưng đảm bảo độ ổn định và đáng tin cậy, đặc biệt với các câu hỏi về sản phẩm, chính sách hoặc hướng dẫn sử dụng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> 
- Khối lượng sử dụng mỗi ngày là **10,5 triệu output token** (10.000 × 3 × 350). Với giá API hiện tại, **GPT-4o đắt hơn GPT-4o-mini khoảng 16,7 lần** (105 USD/ngày so với 6,3 USD/ngày, chỉ tính output token).
- **GPT-4o** phù hợp cho các tác vụ cần độ chính xác và suy luận cao (phân tích tài liệu, hỗ trợ chuyên môn), trong khi **GPT-4o-mini** phù hợp với chatbot FAQ hoặc chăm sóc khách hàng để tối ưu chi phí.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> 
Hai phản hồi khác nhau rõ rệt về phong cách và mức độ chi tiết. Với system prompt **giáo viên tiểu học**, mô hình sử dụng từ ngữ đơn giản, ví dụ gần gũi (cuốn sổ tay, ghi điểm) để trẻ em dễ hiểu. Trong khi đó, system prompt **chuyên gia tài chính** tạo ra câu trả lời dài hơn, sử dụng nhiều thuật ngữ kỹ thuật như *Distributed Ledger Technology (DLT)*, *hash*, *immutability* và trình bày theo cấu trúc chuyên môn. Điều này cho thấy **system prompt định hướng vai trò, ngôn ngữ, độ sâu kiến thức và cách diễn đạt của mô hình**.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> 
Số token thực tế (đếm bằng `tiktoken`) thường **cao hơn khoảng 10–30%** so với cách ước lượng `số từ / 0.75`, tùy nội dung. Nguyên nhân là tiếng Việt có nhiều từ đa âm tiết, dấu thanh và ký tự Unicode nên tokenizer thường tách thành nhiều token hơn so với tiếng Anh có cùng số từ. Vì vậy, việc sử dụng `tiktoken` sẽ cho kết quả chính xác hơn khi ước tính chi phí API.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> 
Streaming quan trọng nhất khi mô hình tạo câu trả lời dài hoặc cần phản hồi nhanh, vì người dùng có thể đọc nội dung ngay khi mô hình đang sinh, giúp giảm cảm giác chờ đợi. Ngược lại, non-streaming phù hợp khi câu trả lời ngắn, cần xử lý hoàn chỉnh trước khi hiển thị hoặc khi ứng dụng yêu cầu nhận toàn bộ kết quả một lần.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> 
Exponential backoff giúp giảm áp lực lên API bằng cách tăng dần thời gian chờ giữa các lần retry, tạo cơ hội để hệ thống phục hồi khi quá tải. Nếu hàng nghìn client đều retry với cùng một delay cố định, chúng sẽ gửi yêu cầu lại cùng lúc, gây ra hiện tượng **retry storm**, làm API tiếp tục quá tải và khó phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> 
- **Persona đã chọn:** Trợ giảng AI thân thiện, hỗ trợ giải đáp thắc mắc lập trình cho học viên.
- **System prompt:** "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt."
- **Giải thích từ ngữ quan trọng:**
  1. *"Trả lời ngắn gọn bằng tiếng Việt"*: Tiết kiệm số lượng token (tối ưu chi phí API) và giữ cho giao diện CLI dễ đọc, giúp người học nhanh chóng nắm bắt ý chính mà không bị ngợp thông tin.
  2. *"Bằng tiếng Việt"*: Đảm bảo mô hình luôn phản hồi bằng ngôn ngữ học tập của sinh viên, ngay cả khi câu hỏi chứa thuật ngữ kỹ thuật tiếng Anh.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> 
- **Hạn chế lớn nhất:** Trợ lý chưa có bộ nhớ dài hạn giữa các phiên làm việc (khi tắt ứng dụng sẽ mất hoàn toàn dữ liệu) và history trong phiên bị giới hạn ở 3 lượt gần nhất, làm mất các chi tiết quan trọng đã trao đổi ở đầu phiên chat dài.
- **Đề xuất cải thiện:** Triển khai cơ chế **Conversation Summarization (Tóm tắt hội thoại)**:
  - Khi history đạt quá 6 tin nhắn, thay vì xóa hoàn toàn các tin nhắn cũ, hệ thống sẽ gọi LLM tóm tắt các lượt thoại cũ thành một đoạn `system_summary`.
  - Đoạn tóm tắt này được giữ lại trong system prompt cho các lượt tiếp theo. Nhờ đó trợ lý vừa nắm được ngữ cảnh cốt lõi từ đầu phiên, vừa duy trì được số lượng token ở mức hợp lý.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
