# Individual reflection — Phan Tuấn Minh (2A202600422)

## 1. Role
Product thinker + Canvas lead. Phụ trách xây dựng AI Canvas và phân tích failure modes.

## 2. Đóng góp cụ thể
- Tham gia đóng góp bộ phần Canvas (Value / Trust / Feasibility) — phân tích pain point khách hàng và doanh nghiệp, justify lý do chọn Augmentation thay vì Automation
- Phân tích và liệt kê 3 failure mode chính: Khuyến mãi chồng chéo, Dữ liệu outdated, Hallucination — mỗi cái đều có trigger, hậu quả, và mitigation cụ thể
- Thiết kế learning signal loop: explicit feedback (thumbs up/down) và implicit signal (tỉ lệ bấm "Gặp Sales")

## 3. SPEC mạnh/yếu
- **Mạnh nhất:** Failure Mode Library — nhóm đã nghĩ ra được case "Khuyến mãi chồng chéo" 
- **Yếu nhất:** Phần learning signal còn chung chung. Nên làm rõ hơn: data hội thoại lưu dưới format nào? Pipeline từ feedback → update RAG diễn ra tần suất bao lâu một lần?

## 4. Đóng góp khác
- Cùng Linh brainstorm edge cases cho failure modes, đặc biệt case khách cố tình test AI bằng câu hỏi sai (ví dụ: "VinFast đang giảm 500tr đúng không?")
- Review lại Canvas của cả nhóm trước khi nộp SPEC draft

## 5. Điều học được
Trước hackathon nghĩ "Trust" trong Canvas chỉ là vấn đề kỹ thuật (AI có accurate không). Sau khi viết mới hiểu: Trust là UX decision — khách hàng cần biết AI có thể sai, và phải có đường thoát rõ ràng (nút "Kết nối Sales", citation nguồn). Thiết kế Trust tốt không phải là làm AI sai ít hơn, mà là làm cho sai có thể phục hồi được.

## 6. Nếu làm lại
Sẽ viết Canvas sớm hơn và chia sẻ với cả nhóm ngay từ đầu để mọi người cùng align trước khi chia việc. Lần này phần Canvas và User Stories viết song song nên có một vài chỗ không khớp nhau về assumption ban đầu, phải sửa lại mất thêm thời gian.

## 7. AI giúp gì / AI sai gì
- **Giúp:** Dùng Claude để brainstorm failure modes — nó gợi ý thêm case "data poisoning" (ai đó cố tình đưa thông tin sai vào RAG) mà nhóm chưa nghĩ đến. Hữu ích như một "red team" nhanh.
- **Sai/mislead:** Claude đề xuất thêm metric "Customer Satisfaction Score (CSAT)" vào Eval — nghe hợp lý nhưng thực ra với chatbot tư vấn giá, CSAT phụ thuộc nhiều vào Sales chứ không phản ánh chất lượng AI. Bài học: AI gợi ý metric hay nhưng không hiểu context business cụ thể.
