# Individual Reflection — Phạm Việt Hoàng (2A202600274)

## 1. Role cụ thể trong nhóm

**Full-stack Integration & Prompt Engineer.**
Tôi chịu trách nhiệm chính trong việc xây dựng cầu nối (API) giữa bộ não của Agent và giao diện người dùng (UI), đồng thời tinh chỉnh logic phản hồi của hệ thống và testing prompt.

## 2. Phần phụ trách cụ thể

Tôi đảm nhận các đầu việc có tính kỹ thuật then chốt để sản phẩm có thể chạy thực tế (working prototype):

- **API Integration:** Viết FastAPI kết nối LangGraph Agent với Frontend, đảm bảo dữ liệu truyền tải mượt mà, không bị lag.
- **Dynamic UI Trigger:** Thiết kế tính năng "context-aware" — khi chatbot nhắc đến một dòng xe nhất định (ví dụ: VF8, VF9), hệ thống sẽ tự động bắt từ khóa để hiển thị hình ảnh mẫu xe tương ứng ngay bên cạnh box chat, tăng trải nghiệm thị giác cho người dùng.
- **RAG Testing & System Prompt:** Trực tiếp tham gia kiểm thử dữ liệu từ file tài liệu chính sách (RAG) và tinh chỉnh System Prompt để Agent trả lời sắc sảo, đúng hướng.

## 3. SPEC phần nào mạnh nhất, phần nào yếu nhất? Vì sao?

- **Mạnh nhất — User Stories 4 paths (Phần 2):**
  Đây là phần được thiết kế kỹ nhất. Mỗi path không chỉ liệt kê luồng mà còn ghi rõ "UX điểm quan trọng" — ví dụ: không hiện loading trắng khi Agent đang suy nghĩ, suggestion chips biến mất ngay sau tin nhắn đầu để tránh rối mắt, và chặn việc AI hứa hẹn viển vông kiểu "Em sẽ tra cứu...". Những chi tiết này xuất phát từ việc mô phỏng hành vi người dùng thực, mang lại cảm giác sản phẩm rất "lỳ" và thực tế.
- **Yêu nhất — ROI (Phần 5):**
  Ba kịch bản Conservative / Realistic / Optimistic về cơ bản chỉ khác nhau ở số lượng showroom triển khai và tỷ lệ người dùng sử dụng. Các giả định nền (chi phí API, cấu trúc đội ngũ) còn khá giống nhau. Để thuyết phục hơn, chúng tôi cần các giả định mang tính chiến lược khác biệt — ví dụ: bản Conservative chỉ là pilot 1 showroom để đo lường, còn bản Optimistic phải tính đến việc tích hợp sâu để khách đặt lịch lái thử ngay trên chat.

## 4. Đóng góp cụ thể khác

- Hỗ trợ team UI xử lý các lỗi hiển thị trên nhiều kích thước màn hình.
- Sửa lỗi logic trong bộ công cụ (Tools) của Agent khi gặp các câu hỏi lắt léo về so sánh thông số kỹ thuật.
- **Trực tiếp trình bày (Demo):** Thủ vai người dùng để show toàn bộ tính năng vượt trội của chatbot trước hội đồng và các nhóm khác.

## 5. 1 điều học được trong hackathon mà trước đó chưa biết

Tôi đã được rèn luyện khả năng **quản lý áp lực và phân bổ tiến độ trong thời gian cực ngắn**. Trước đây tôi thường tập trung làm cho xong code, nhưng qua hackathon, tôi học được rằng việc phối hợp nhịp nhàng giữa các thành viên và biết lúc nào cần "freeze code" để tập trung vào Narrative (câu chuyện demo) là yếu tố quyết định sự thành công của sản phẩm.

## 6. Nếu làm lại, đổi gì?

Tôi sẽ tập trung sâu hơn vào phần **Agent Logic & Accuracy**. Có những lúc tôi đã dành quá nhiều thời gian để "mông má" cho UI thật đẹp, trong khi đáng lẽ nên dùng thời gian đó để tinh chỉnh thêm các trường hợp Agent xử lý dữ liệu RAG khó, giúp câu trả lời chắc chắn hơn nữa. Tôi sẽ tránh việc lan man vào thẩm mỹ UI khi lõi AI chưa đạt mức 100% mong muốn.

## 7. AI giúp gì? AI sai/mislead ở đâu?

- **Giúp:** AI (ChatGPT/Gemini) giúp tôi debug nhanh các đoạn code kết nối API và gợi ý các kịch bản phòng thủ (security) khi người dùng cố tình tấn công bằng prompt (Prompt Injection) hoặc hỏi các chủ đề ngoài lề.
- **Sai:** Có một vài lần khi tôi yêu cầu AI tính toán giá xe cộng với các ưu đãi phức tạp của VinFast, AI đã tính toán nhầm các con số hoặc nhầm lẫn giữa các phiên bản thuê pin/mua pin. Tôi đã phải kiểm tra lại bằng tay và "hard-code" logic tính toán hoặc dùng few-shot prompting để AI không bị sai lệch ở phần này.
