# Ngày 1 — Bài Tập & Phản Ánh (side lab TUỲ CHỌN)

> Side lab này **không** thuộc điểm chính của Day 01 (xem [RUBRIC.md](../RUBRIC.md) mục Bonus). Model: `gpt-6.1-terra` (lớn) và `gpt-6-luna` (nhỏ). `gpt-4o` / `gpt-4o-mini` bị gỡ khỏi API ngày 14/01/2027 ([nguồn](https://developers.openai.com/api/docs/deprecations)). Tên khoá `gpt4o_*` / `mini_*` trong kết quả là hợp đồng của test, giữ nguyên để tương thích: đọc là "model lớn" / "model nhỏ".
## Nền Tảng LLM API | Phiếu Thực Hành

**Thời lượng:** 1:30 giờ  
**Cấu trúc:** Lập trình cốt lõi (60 phút) → Bài tập mở rộng (30 phút)

---

## Phần 1 — Lập Trình Cốt Lõi (0:00–1:00)

Chạy các ví dụ trong Google Colab tại: https://colab.research.google.com/drive/172zCiXpLr1FEXMRCAbmZoqTrKiSkUERm?usp=sharing

Copy `src/optional_llm_api/template.py` thành `src/optional_llm_api/solution/solution.py`, triển khai mọi TODO, rồi chạy từ gốc repo: `pytest tests/test_solution.py -v`. Chưa có `solution/solution.py` thì test tự bỏ qua.

**Điểm kiểm tra:** Sau khi hoàn thành 4 nhiệm vụ, chạy:
```bash
python src/optional_llm_api/solution/solution.py
```
Bạn sẽ thấy output so sánh phản hồi của `gpt-6.1-terra` và `gpt-6-luna`.

---

## Phần 2 — Bài Tập Mở Rộng (1:00–1:30)

### Bài tập 2.1 — Độ Nhạy Của Temperature
Gọi `call_openai` với các giá trị temperature 0.0, 0.5, 1.0 và 1.5 sử dụng prompt **"Hãy kể cho tôi một sự thật thú vị về Việt Nam."** (nhớ gửi `reasoning_effort="none"`: model GPT-6 chỉ nhận `temperature` khi tắt suy luận)

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Với `temperature=0.0`, câu trả lời bốn câu hỏi tương đối ổn định và ít thay đổi vì mô hình giảm tính ngẫu nhiên khi chọn từ. Khi tăng lên `0.5` và `1.0`, câu chuyện có thể khác về diễn đạt hoặc ví dụ nhưng vẫn giữ được độ chính xác và đúng chủ đề. Mức `1.5` dễ tạo câu văn đa dạng hơn, nhưng cũng tăng nguy cơ lan man hoặc sử dụng chi tiết ít tin cậy; vì vậy mức này không phù hợp với câu trả lời cần độ chính xác cao.

**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Mình sẽ đặt `temperature=0.2` hoặc `0.3`. Cấu hình này vẫn cho câu trả lời tự nhiên, nhưng giữ câu trả lời gần hành chính, nhất quán và ít bịa đặt hơn. Nếu chatbot chỉ trả thông tin và hướng dẫn quy trình, `temperature=0` là lựa chọn thận trọng hơn.

---

### Bài tập 2.2 — Đánh Đổi Chi Phí
Xem xét kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người thực hiện 3 lần gọi API, mỗi lần trung bình ~350 token.

**Ước tính xem `gpt-6.1-terra` đắt hơn `gpt-6-luna` bao nhiêu lần cho workload này** (giá 11/2026: terra $1.00 / $5.00, luna $0.10 / $0.50 mỗi 1M token input / output):
> Mỗi ngày có `10.000 × 3 × 350 = 10.500.000` token, tương đương `10,5` triệu token. Cả hai model đều giữ tỷ lệ giá Terra/Luna khoảng 10 lần ở input (`$1/$0,10 = 10×`) và output (`$5/$0,50 = 10×`), nên tổng chi phí Terra khoảng **10 lần** Luna, không phụ thuộc xấp xỉ vào việc phân bố input/output.

> Nếu mỗi lời gọi có 300 token input và 50 token output, chi phí Terra/ngày là `9M × $1/1M + 1,5M × $5/1M = $16,50`; Luna là `9M × $0,10/1M + 1,5M × $0,50/1M = $1,65`. Nghĩa là Terra khoảng `$16,50/ngày` và Luna khoảng `$1,65/ngày`, Terra đắt hơn khoảng 10 lần. Nếu token phân bổ khác thì con số tuyệt đối đổi, nhưng mức chênh lệch vẫn gần 10 lần.

**Mô tả một trường hợp mà chi phí cao hơn của `gpt-6.1-terra` là xứng đáng, và một trường hợp `gpt-6-luna` là lựa chọn tốt hơn:**
> Terra xứng đáng khi xử lý tác vụ cần suy luận hoặc độ chính xác cao, chẳng hạn tư vấn về điều khoản, phân tích case khó, hỗ trợ kỹ thuật phức tạp hoặc phản hồi của chatbot điều trị rủi ro cao; sai một tin có thể mất nhiều chi phí hơn phần chênh lệch API. Luna phù hợp hơn cho tác vụ có câu trả lời ngắn, lặp lại và rủi ro thấp, như phân loại tin nhắn spam/ham, tóm tắt ngắn, tìm kiếm FAQ hoặc trả lời câu hỏi có mẫu; có thể thêm router để Luna xử lý trường hợp đơn giản và Terra chỉ xử lý trường hợp cần độ chính xác cao hơn.

---

### Bài tập 2.3 — Trải Nghiệm Người Dùng với Streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất với chatbot hội thoại, hỗ trợ khách hàng, viết nội dung dài hoặc tác vụ người dùng cần đọc kết quả ngay; chữ xuất hiện từng phần trong lúc chờ làm `time to first token` ngắn hơn trong cảm nhận, giảm cảm giác treo máy dù độ trễ toàn bộ không thay đổi. Non-streaming phù hợp hơn khi hệ thống cần xử lý máy-máy, batch, phân loại nhãn, JSON có cấu trúc, retry hoặc kiểm tra kết quả trước khi hiển thị, nơi toàn bộ phản hồi phải được parse hoặc xác thực mới có thể dùng.


## Danh Sách Kiểm Tra Nộp Bài
- [ ] Tất cả tests pass: `pytest tests/test_solution.py -v`
- [ ] `call_openai` đã triển khai và kiểm thử
- [ ] `call_openai_mini` đã triển khai và kiểm thử
- [ ] `compare_models` đã triển khai và kiểm thử
- [ ] `streaming_chatbot` đã triển khai và kiểm thử
- [ ] `retry_with_backoff` đã triển khai và kiểm thử
- [ ] `batch_compare` đã triển khai và kiểm thử
- [ ] `format_comparison_table` đã triển khai và kiểm thử
- [ ] `exercises.md` đã điền đầy đủ
- [ ] Sao chép bài làm vào folder `solution` và đặt tên theo quy định 
