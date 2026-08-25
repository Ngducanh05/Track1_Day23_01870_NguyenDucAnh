# AI Support Log — Day 23

**Học viên:** Nguyễn Đức Anh  
**MHV:** 01870  
**Dự án:** AI Deadline Copilot

## AI đã giúp tôi ở đâu?

- Đọc brief Day 23 và đối chiếu cấu trúc repository với yêu cầu nộp bài.
- Kiểm tra sự khác nhau giữa thao tác giao diện, core action và core value event.
- Brainstorm candidate event theo dạng `object_action`.
- Phản biện retention để tránh dùng D7/weekly chỉ vì dashboard quen dùng.
- Gợi ý event-to-metric mapping và acceptance criteria chống duplicate/retry.
- Gợi ý counter-metrics để tránh tối ưu số task completed theo hướng bị game.

## AI sai, hời hợt hoặc có giới hạn ở đâu?

- AI có thể đề xuất một core action, cadence và metric hypothesis nghe hợp lý nhưng không có quyền quyết định thay học viên theo luật của lab.
- Nếu chỉ tối ưu `study_task_completed`, AI dễ bỏ qua bẫy chia task quá nhỏ; vì vậy cần counter-metric về missed deadline, reopen và micro-task inflation.
- AI không có dữ liệu sản phẩm thật nên không được tự bịa ngưỡng activation 24h/7d hoặc benchmark retention.

## Tôi đã tự sửa hoặc quyết định lại điều gì?

> **HỌC VIÊN TỰ VIẾT TRƯỚC KHI NỘP.**  
> Viết ngắn 2–4 dòng, chỉ ghi quyết định thực sự của mình. Không copy câu AI.

Gợi ý để tự điền:
- Tôi chọn/không chọn `study_task_completed` làm core value event vì...
- Tôi giữ/đổi cadence project-based vì...
- Tôi rewrite metric hypothesis thành...
- Tôi thêm/bỏ metric ... vì...

## Cam kết trước khi nộp

- [ ] Tôi đã tự quyết định core action.
- [ ] Tôi đã tự quyết định cadence.
- [ ] Tôi đã tự viết metric hypothesis.
- [ ] Tôi đã tự viết rationale/reflection.
- [ ] Những phần AI hỗ trợ đã được khai báo ở trên.
