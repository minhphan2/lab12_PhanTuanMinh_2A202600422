# Individual Reflection — Bùi Minh Ngọc (2A202600354)

## 1. Role

**Prompt Engineer + QA.** Phụ trách thiết kế và iterate system prompt cho ViVi, đồng thời xây dựng bộ test cases để đánh giá chất lượng hội thoại của agent.

---

## 2. Đóng góp cụ thể

- **Thiết kế và iterate system prompt:** Viết system prompt cho ViVi gồm persona (xưng "em", gọi "Anh/Chị"), rules cứng chống hallucination (tuyệt đối không tự suy diễn ưu đãi), default location Hà Nội, và failure routing khi gặp câu hỏi ngoài scope; qua ít nhất 3 vòng chỉnh sửa dựa trên kết quả test thực tế.
- **Xây dựng bộ test cases và ghi log kết quả:** Thiết kế tập 10+ câu hỏi test bao phủ các scenario: hỏi đủ thông tin, hỏi thiếu model xe, hỏi out-of-scope, hỏi leading ("VinFast giảm 500 triệu đúng không?") — kết quả ghi vào `test_results.md` với cột Expected vs Actual.
- **Fix lỗi prompt dựa trên test fail:** Phát hiện agent gọi `calculate_rolling_price` với sai format tham số `discounts`, truy ngược ra system prompt thiếu ví dụ cụ thể về format `"MÃ:SỐ_TIỀN"` và bổ sung vào — sau fix, 5/5 test case liên quan đều pass.

---

## 3. SPEC phần nào mạnh nhất / yếu nhất?

**Mạnh nhất — User Stories 4 paths (Phần 2):**  
Đây là phần được thiết kế kỹ nhất. Mỗi path không chỉ liệt kê luồng mà còn ghi rõ "UX điểm quan trọng" — ví dụ: không hiện loading trắng, suggestion chips biến mất sau tin nhắn đầu, AI không bao giờ gửi câu hứa hẹn kiểu "Em sẽ tra cứu...". Những chi tiết này xuất phát từ việc mô phỏng hành vi người dùng thực, không chỉ nghĩ ra flow lý thuyết.

**Yếu nhất — ROI / Phần 5:**  
Ba kịch bản Conservative / Realistic / Optimistic về cơ bản chỉ khác nhau ở số lượng showroom và tỷ lệ người dùng chấp nhận bot. Assumption nền (chi phí API, cấu trúc đội ngũ, hành vi khách hàng) vẫn còn na ná nhau. Để thuyết phục hơn, mỗi kịch bản cần một assumption khác biệt thực sự — ví dụ: conservative nên giả định thị trường VN chưa quen chatbot + chỉ pilot 1 showroom, còn optimistic cần thêm assumption về tích hợp đặt lịch lái thử trực tiếp từ chat.

---

## 4. Đóng góp khác

- **Hỗ trợ viết SPEC phần 2 — User Stories 4 paths:** Cùng Phan Tuấn Minh thiết kế 4 kịch bản hội thoại (Happy, Low-confidence, Failure/Recovery, Correction) với flow và UX note chi tiết; các path này sau đó được dùng trực tiếp làm input để xây dựng test cases.
- **Viết constraint chống hallucination trong system prompt:** Sau khi team phát hiện AI có xu hướng confirm câu hỏi leading của khách, bổ sung rule cứng và few-shot example vào prompt để agent từ chối rõ ràng thay vì xác nhận thông tin không có trong `Sale.md`.

---

## 5. Điều học được trong hackathon mà trước đó chưa biết

Trước hackathon tôi nghĩ LLM với `temperature=0` là "chính xác hơn" nên nên dùng mặc định. Sau khi làm mới hiểu: `temperature=0` chỉ nên áp dụng cho **tool extraction** (extract ưu đãi từ văn bản) để tối thiểu hallucination khi làm việc với số tiền cụ thể — còn agent chính cần giữ temperature mặc định để linh hoạt trong hội thoại tự nhiên. Đây là quyết định **product**, không phải chỉ engineering: đặt sai temperature ở sai chỗ có thể khiến AI hoặc cứng nhắc khi chat, hoặc "sáng tạo" khi tính tiền — cả hai đều tệ theo cách khác nhau.

---

## 6. Nếu làm lại, đổi gì?

Sẽ viết test cases **trước khi viết system prompt** — tức là ngay khi có SPEC phần 2 (User Stories), chuyển luôn 4 path đó thành file test cases với Expected Output cụ thể. Thực tế nhóm viết prompt trước, rồi mới test, nên mỗi lần test fail lại không rõ lỗi do prompt sai hay do flow logic sai. Nếu có acceptance test từ đầu, việc debug sẽ nhanh hơn ít nhất 2–3 lần vì biết chính xác muốn AI output gì trước khi bắt đầu viết prompt.

---

## 7. AI giúp gì? AI sai / mislead ở đâu?

**AI giúp:**  
Dùng Claude để brainstorm Failure Modes — nó gợi ý thêm case "khách hỏi leading" (*"VinFast đang giảm 500 triệu đúng không?"*) mà nhóm chưa nghĩ tới (FM-03). Đây trở thành một trong những failure mode quan trọng nhất vì nó test trực tiếp khả năng chống hallucination của hệ thống.  
Ngoài ra, dùng AI để dự thảo nhanh format bảng ưu đãi trong `Sale.md` từ dữ liệu thô — tiết kiệm khoảng 1–2 giờ format thủ công.

**AI sai / mislead:**  
Khi hỏi AI về kiến trúc, nó gợi ý dùng **vector database (FAISS)** để lưu và tìm kiếm ưu đãi trong `Sale.md`. Nghe có vẻ "đúng theo lý thuyết RAG", nhưng thực tế file Sale.md chỉ có ~200 dòng — embedding + vector search hoàn toàn overkill, chậm hơn và phức tạp hơn so với đọc file trực tiếp rồi dùng LLM extract. Nếu không nhận ra điều này sớm, nhóm đã mất nửa ngày setup FAISS cho một bài toán không cần đến nó. Bài học: AI có xu hướng gợi ý giải pháp "theo sách giáo khoa" thay vì giải pháp đơn giản nhất phù hợp với scale thực tế.
