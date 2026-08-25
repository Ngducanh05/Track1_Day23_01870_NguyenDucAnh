# Day 23 — Metrics Pack

**Học viên:** Nguyễn Đức Anh  
**MHV:** 01870  
**Dự án:** AI Deadline Copilot  
**Use case:** Một sinh viên theo dõi và hoàn thành các next action của một assignment trước deadline.

> **Quy tắc sử dụng bản này:** Các phần đánh dấu **USER CONFIRMATION REQUIRED** là bản tham chiếu do AI hỗ trợ brainstorm. Trước khi nộp, học viên phải tự bảo vệ, chỉnh/rewrite bằng quyết định của mình theo đúng brief Day 23.

---

# 00 — Phạm vi

| Thành phần | Nội dung |
|---|---|
| **Dự án** | AI Deadline Copilot — trợ lý giúp biến một assignment có deadline thành kế hoạch hành động có thể thực hiện và theo dõi. |
| **Persona** | Sinh viên đại học đang xử lý nhiều assignment có deadline song song. |
| **Core job** | “Tôi cần biết việc cụ thể tiếp theo phải làm và hoàn thành từng phần đủ sớm để không dồn toàn bộ assignment sát deadline.” |
| **Use case duy nhất** | Theo dõi và hoàn thành các next action của **một assignment đang active** trước deadline. |
| **Phạm vi loại trừ** | Không phân tích toàn bộ học tập, lịch cá nhân, điểm số, chat AI tổng quát hay mọi loại deadline. |

### Boundary của use case

- Bắt đầu khi một assignment đã được đưa vào hệ thống và có deadline.
- Giá trị không được tính chỉ vì AI sinh ra kế hoạch.
- Giá trị chỉ tiến gần hơn khi sinh viên thực sự hoàn thành một đơn vị công việc của assignment.
- Kết thúc use case khi assignment hoàn thành, bị hủy, hoặc deadline đã qua.

---

# 01 — Core Action

## 01.1 Phân biệt bốn khái niệm

| Khái niệm | Câu trả lời cho AI Deadline Copilot |
|---|---|
| **Core job** | Hoàn thành assignment theo tiến độ đủ sớm để tránh dồn việc sát deadline. |
| **Core action** | **[USER CONFIRMATION REQUIRED]** Sinh viên hoàn thành một next action đã được lên kế hoạch cho assignment. |
| **Core value** | Assignment có tiến triển thực, khối lượng việc còn lại giảm và bước tiếp theo trở nên rõ ràng hơn. |
| **Core value event** | `study_task_completed` |

> **Vì sao không chọn `ask_ai`, `app_opened`, `login_succeeded`?**  
> Các event đó chỉ chứng minh user tương tác với giao diện/hệ thống. Chúng không chứng minh assignment đã tiến triển.

## 01.2 Core Action Card

| Thành phần | Câu trả lời |
|---|---|
| **Target user** | Sinh viên có ít nhất một assignment đang active. |
| **Core job** | Hoàn thành assignment theo từng bước đủ sớm để tránh dồn việc sát deadline. |
| **Core action** | **[USER CONFIRMATION REQUIRED]** Hoàn thành một next action đã được lên kế hoạch của assignment. |
| **Object** | `study_task` / `next_action`, luôn gắn với một `assignment_id`. |
| **Preconditions** | Assignment tồn tại; có deadline hợp lệ; next action thuộc assignment; task chưa ở trạng thái completed. |
| **Completion rule** | Action hoàn tất khi trạng thái task chuyển từ trạng thái chưa hoàn thành sang `completed`, hệ thống lưu `completed_at`, `user_id`, `assignment_id`, `task_id`. |
| **Core value** | User tạo ra tiến triển quan sát được đối với assignment, giảm phần việc còn lại và có cơ sở để đi tiếp. |
| **Evidence of value** | Một planned task thực sự chuyển sang completed và trạng thái tiến độ của assignment được cập nhật. |
| **Candidate event** | `study_task_completed` |

## 01.3 Tự kiểm 5 tiêu chí

| Tiêu chí | Đạt? | Lý do kiểm tra |
|---|---:|---|
| Gần core value | ✅ | Hoàn thành work unit làm assignment tiến triển thực, khác với chỉ xem/generate kế hoạch. |
| Có thể lặp lại | ✅ | Một assignment có nhiều next action; các assignment mới tạo ra nhu cầu lặp lại. |
| Có thể quan sát | ✅ | Có state transition và timestamp xác định thời điểm hoàn tất. |
| Có ý nghĩa | ✅ | Nhiều core action hợp lệ thường phản ánh tiến độ tốt hơn, nếu không làm xấu counter-metrics. |
| Có thể tác động | ✅ | Team có thể cải thiện decomposition, clarity, scheduling, prioritization và friction để tăng xác suất hoàn thành. |

**Gate 1:** 5/5 theo bản tham chiếu.

### Câu hỏi học viên phải tự bảo vệ

1. Nếu AI tự đánh dấu task hoàn tất thì có còn là core action của user không?
2. Nếu user chia một việc 30 phút thành 20 task cực nhỏ, số `study_task_completed` tăng có còn là value không?
3. Có nên dùng `assignment_completed` thay cho `study_task_completed` làm core value event không? Vì sao?

---

# 02 — Nature & Cadence

## 02.1 Action Nature Card

| Thành phần | Phân tích |
|---|---|
| **Actor** | `user_id` của sinh viên; object-level analysis dùng thêm `assignment_id`. |
| **Intent** | Tạo tiến triển thật cho assignment đang active và giảm rủi ro dồn việc trước deadline. |
| **Trigger** | Chủ yếu từ nhu cầu tự nhiên: có assignment mới, còn work unit chưa xong, tới thời điểm học/làm bài, hoặc một prerequisite đã hoàn tất. |
| **Effort** | Không cố định. Một next action có thể mất từ vài phút tới một phiên tập trung dài; cognitive effort cao hơn thao tác UI đơn giản. |
| **Value timing** | Có value ngay (một phần việc đã xong) và value tích lũy (assignment tiến gần completion). |
| **State** | Task completion, `completed_at`, assignment progress, remaining tasks và next actionable item được lưu lại. |
| **Dependency** | Phụ thuộc deadline, độ lớn assignment, prerequisite giữa task, thời gian rảnh và đôi khi feedback từ giảng viên/nhóm. |
| **Repeat condition** | Còn next action chưa hoàn thành trong assignment hiện tại hoặc xuất hiện assignment mới. |

## 02.2 Dạng hành vi

**[USER CONFIRMATION REQUIRED]** Bản tham chiếu: **progress + project-based**.

Lý do tham chiếu:
- User không có lý do tự nhiên để hoàn thành assignment task “mỗi ngày” trong mọi hoàn cảnh.
- Nhu cầu tăng/giảm theo số assignment đang active, deadline và kích thước project.
- Frequency cao hơn không luôn tốt hơn: chia task quá nhỏ có thể làm số event tăng nhưng value không tăng tương ứng.

## 02.3 Kết luận cadence

> **[USER CONFIRMATION REQUIRED — HỌC VIÊN REWRITE TRƯỚC KHI NỘP]**  
> Đối với sinh viên có một assignment đang active, core action **hoàn thành một next action đã lên kế hoạch** thường xuất hiện **nhiều lần trong vòng đời assignment, tập trung theo các work session và deadline** vì **mỗi lần hoàn thành một work unit làm giảm phần việc còn lại cho tới khi assignment kết thúc**. Do đó, nhịp đo phù hợp là **theo active-assignment lifecycle (project-based), có thể roll-up theo tuần chỉ để quan sát vận hành** ở cấp **user × assignment**.

**Gate 2:** Nhịp tham chiếu xuất phát từ nature của assignment, không bắt đầu từ DAU/D7.

---

# 03 — Metric System

## 03.1 Activation

### Definition

- **Start event:** `assignment_plan_created`
- **Activation event:** lần đầu `study_task_completed` cho cùng `assignment_id`
- **Time window:** **trước deadline của assignment và trong phase đầu của vòng đời plan**.

### Cách triển khai tracking

Để metric tính được mà không bịa benchmark, lưu:
- `plan_created_at`
- `assignment_due_at`
- `task_completed_at`
- `assignment_id`
- `user_id`

Sau khi có dữ liệu thật, team mới chọn ngưỡng activation tối ưu (ví dụ percentile hoặc bucket theo assignment duration) thay vì áp một con số 24h/7d không có căn cứ.

### Activation metric đề xuất

`Activation Rate = số user-assignment có >= 1 study_task_completed trong activation window / số user-assignment có assignment_plan_created hợp lệ`

> Đây là metric candidate. Ngưỡng thời gian cuối cùng phải được calibrate bằng dữ liệu thật theo độ dài assignment.

## 03.2 Engagement

Chọn tối đa hai góc:

### 1. Frequency — qualified core actions per active assignment

`Qualified Task Completions per Active Assignment`

Đếm số `study_task_completed` hợp lệ trong vòng đời một assignment.

### 2. Depth — planned work completion ratio

`Planned Task Completion Ratio = số planned tasks completed / tổng planned tasks hợp lệ của assignment`

Depth giúp tránh nhìn số event thuần khi hai assignment có quy mô task khác nhau.

## 03.3 North Star Metric

### NSM candidate

**Qualified Next Actions Completed per Active Assignment**

- **Unit of value:** một `study_task_completed`.
- **Quality threshold:** task thuộc plan hợp lệ, completion là state transition thật, xảy ra trước `assignment_due_at`, không phải duplicate/retry.
- **Frequency:** số qualified completions trong một active-assignment lifecycle.

### Công thức

`NSM = count(qualified study_task_completed) / count(active assignment lifecycles)`

### Tại sao metric này gần value hơn “số lượt hỏi AI”?

Vì hệ thống chỉ nhận credit khi công việc của assignment thực sự chuyển sang hoàn tất, không phải khi AI tạo output hoặc user mở app.

## 03.4 Leading Indicators

| Leading indicator | Định nghĩa | Vì sao có thể dự báo core action lặp lại |
|---|---|---|
| **Plan-to-first-completion rate** | Tỷ lệ assignment plan dẫn tới ít nhất một `study_task_completed`. | Nếu plan đủ actionable để tạo first value, khả năng user dùng tiếp loop progress cao hơn. |
| **Next-action scheduled coverage** | Tỷ lệ active assignments luôn có ít nhất một next action hợp lệ chưa hoàn thành. | Có bước tiếp theo rõ ràng làm giảm restart cost ở phiên sau. |
| **Task start → completion conversion** | `study_task_completed / study_task_started` theo user-assignment. | Nhiều task được bắt đầu nhưng bỏ dở báo hiệu decomposition hoặc effort không phù hợp. |

## 03.5 Counter-metrics

| Counter-metric | Bẫy được phát hiện |
|---|---|
| **Assignment missed-deadline rate** | Core action tăng nhưng outcome cuối vẫn xấu: sinh viên vẫn trễ deadline. |
| **Task reopen rate** | User đánh completed rồi mở lại nhiều, cho thấy completion không phản ánh work thật sự hoàn tất. |
| **Micro-task inflation rate** | Plan bị chia thành quá nhiều task cực nhỏ để làm NSM tăng giả tạo. |

### Counter-metric ưu tiên

**Assignment missed-deadline rate** phải không xấu đi khi NSM tăng.

**Gate 3 (metric):**
- Activation có start + activation event + window logic.
- Engagement chỉ dùng 2 góc: frequency + depth.
- NSM có value unit + quality threshold + frequency.
- Có counter-metric chống gaming.

---

# 04 — Retention Definition

## 04.1 Retention đủ 6 thành phần

| Thành phần | Định nghĩa |
|---|---|
| **Unit** | `user_id` (sinh viên). |
| **Cohort entry** | User nhận first value: có `study_task_completed` đầu tiên trong cohort assignment. |
| **Return event** | Có ít nhất một `study_task_completed` trên một **assignment_id khác** ở chu kỳ assignment kế tiếp. |
| **Window** | **Project-based custom bracket:** từ lúc assignment kế tiếp được tạo/plan cho tới deadline của assignment đó. Không dùng D7 mặc định. |
| **Threshold** | Ít nhất 1 qualified `study_task_completed` trong next eligible assignment cycle. |
| **Segment** | Sinh viên có tối thiểu 2 assignment hợp lệ trong observation period; loại internal/test/bot accounts. |

## 04.2 Project-cycle retention

### Cách xác định cycle

- **Cycle 1:** assignment nơi user nhận first value.
- **Cycle 2:** assignment hợp lệ kế tiếp của cùng user, có `assignment_id` khác.
- **Retained:** Cycle 2 có ít nhất một qualified `study_task_completed`.

### Công thức

`Project-cycle retention = retained users / eligible users with a next assignment cycle`

### Vì sao không dùng D7 retention?

D7 sẽ phạt một sinh viên chỉ vì tuần đó họ không có assignment cần xử lý. Điều đó trộn **absence of need** với **product churn**.

## 04.3 Ba mốc để đánh giá retention

1. **Natural cycle:** assignment lifecycle / next eligible assignment.
2. **Đúng segment:** sinh viên có nhu cầu lặp lại, tức có assignment kế tiếp.
3. **Benchmark category:** **chưa sử dụng** vì brief/source hiện tại không cung cấp benchmark đáng tin cậy; không bịa số.

**Gate 3 (retention):** đủ 6 thành phần và window khớp cadence project-based.

---

# 05 — Product Loop

## 05.1 Loại loop

**Progress loop + project loop**

## 05.2 Loop hai chu kỳ

### Chu kỳ 1

`Natural trigger`
→ Sinh viên có assignment đang active và còn work chưa hoàn thành.

`Core action`
→ Hoàn thành một planned next action.

`Immediate value`
→ Một phần việc thực sự xong; progress tăng; remaining work giảm.

`Saved state / investment`
→ Hệ thống lưu completed task, progress, remaining tasks và next actionable item.

### Chu kỳ 2

`Next natural trigger`
→ Tới work session tiếp theo, prerequisite đã xong, hoặc xuất hiện next action cần làm.

`Core action tiếp theo`
→ Sinh viên hoàn thành next planned action.

`Repeat value`
→ Assignment tiếp tục tiến gần completion; user không phải tái lập kế hoạch từ đầu.

### Qua assignment mới

Khi assignment hiện tại kết thúc, một assignment mới tạo project-cycle mới. Saved history và thói quen làm việc giúp giảm setup/restart cost, nhưng **notification không phải reason to return**.

## 05.3 Metric hypothesis

> **[USER CONFIRMATION REQUIRED — KHÔNG NỘP NGUYÊN VĂN NẾU CHƯA TỰ QUYẾT]**  
> Nếu progress loop này hoạt động, metric **project-cycle retention** sẽ **tăng** trong **các assignment cycle kế tiếp**, vì **saved progress và next-action state làm giảm chi phí bắt đầu lại và giúp user nhanh chóng quay về hành vi tạo value thật**.

### Câu hỏi tự phản biện

- Nếu bỏ toàn bộ notification, user còn lý do quay lại không?
- Saved state nào thật sự làm phiên tiếp theo dễ hơn?
- Loop có thể tăng `study_task_completed` nhưng làm `assignment_missed_deadline_rate` xấu đi không?
- Nếu có, cần sửa decomposition/quality threshold ở đâu?

**Gate 4:** loop có ≥2 chu kỳ; hypothesis trỏ về metric ở Phase 3/4.

---

# 06 — Tracking nhanh

## 06.1 Core events

| Event | Ý nghĩa | Thời điểm ghi nhận chính xác | Metric sử dụng |
|---|---|---|---|
| `assignment_added` | Một assignment hợp lệ đã được user đưa vào hệ thống. | Sau khi record assignment được lưu thành công với `assignment_id`, `user_id`, deadline hợp lệ. | Funnel context, eligibility. |
| `assignment_plan_created` | Một actionable plan cho assignment đã tồn tại và có ít nhất một next action hợp lệ. | Sau transaction tạo plan + tasks commit thành công. | Activation start, plan-to-first-completion. |
| `next_action_scheduled` | Một next action chưa hoàn thành đã có thời điểm/ưu tiên thực thi rõ ràng. | Khi task chuyển từ unscheduled sang scheduled và persistence thành công. | Next-action scheduled coverage. |
| `study_task_started` | User thực sự bắt đầu xử lý một planned task. | Khi task chuyển vào trạng thái `in_progress` lần đầu trong work attempt hợp lệ. | Task start → completion conversion. |
| `study_task_completed` | Core value event: planned task thực sự hoàn thành. | Chỉ khi state transition từ non-completed → `completed` commit thành công. | Activation, engagement, NSM, retention. |
| `study_task_reopened` | Một task từng completed bị chuyển lại trạng thái chưa hoàn tất. | Khi `completed` → non-completed commit thành công. | Task reopen counter-metric. |
| `assignment_completed` | Assignment được user xác nhận hoàn thành. | Khi assignment state chuyển sang `completed` và record lưu thành công. | Outcome / quality validation. |
| `assignment_deadline_missed` | Assignment đi qua deadline mà chưa completed. | Server-side evaluation khi `now > due_at` và state vẫn non-completed; ghi một lần. | Assignment missed-deadline counter-metric. |

## 06.2 Properties tối thiểu nên có

Cho event liên quan task:
- `event_id`
- `event_name`
- `occurred_at`
- `user_id`
- `assignment_id`
- `task_id`
- `task_status_before`
- `task_status_after`
- `assignment_due_at`
- `plan_version`
- `source` (`web`, `mobile`, `api`)
- `is_internal_account`

Cho event assignment:
- `assignment_id`
- `user_id`
- `due_at`
- `status_before`
- `status_after`

## 06.3 Acceptance criteria

### AC-01 — Completion chỉ bắn sau state transition thật

Với mỗi `task_id`, `study_task_completed` chỉ được ghi khi persisted state chuyển từ một trạng thái non-completed sang `completed`. Chỉ bấm nút nhưng transaction thất bại thì **không** được tạo event.

### AC-02 — Reload/retry không tạo duplicate

Một lần completion logic chỉ có một `study_task_completed`. Reload UI, API retry, autosave hoặc retry consumer không được tạo thêm event cho cùng state transition. Dùng idempotency key hoặc unique transition identifier.

### AC-03 — Reopen là event riêng

Nếu task đã completed rồi được mở lại, ghi `study_task_reopened`; lần hoàn thành sau đó được coi là một completion attempt mới nhưng phải giữ `attempt_id`/transition id để phân tích reopen rate.

### AC-04 — Deadline-missed chỉ ghi một lần

`assignment_deadline_missed` chỉ ghi một lần cho mỗi assignment khi deadline đã qua và assignment chưa completed; job chạy lặp không được tạo duplicate.

## 06.4 Event → metric map

| Metric | Event bắt buộc |
|---|---|
| Activation rate | `assignment_plan_created`, `study_task_completed` |
| Qualified task completions / active assignment | `study_task_completed`, assignment lifecycle fields |
| Planned task completion ratio | `assignment_plan_created`, `study_task_completed` |
| Plan-to-first-completion | `assignment_plan_created`, `study_task_completed` |
| Next-action scheduled coverage | `next_action_scheduled` |
| Start → completion conversion | `study_task_started`, `study_task_completed` |
| Project-cycle retention | `study_task_completed` + `assignment_id` |
| Task reopen rate | `study_task_completed`, `study_task_reopened` |
| Missed-deadline rate | `assignment_deadline_missed`, `assignment_completed` |

**Gate 5:** mọi event đều map về metric; không track click không phục vụ metric.

---

# 07 — Revision

## Revision record

| Thời điểm | Thay đổi | Lý do |
|---|---|---|
| Trước khi nộp | **Học viên tự điền nếu có đổi core action/cadence/hypothesis.** | Brief tính rationale cho thay đổi lớn; AI không viết thay phần này. |

---

# Final Gate Checklist

- [x] Core action candidate có actor, object, completion rule.
- [x] Candidate qua 5/5 tiêu chí tự kiểm.
- [x] Cadence candidate đi từ nature, không đi từ dashboard.
- [x] Activation có start event, activation event và window logic.
- [x] Retention đủ unit, cohort entry, return event, window, threshold, segment.
- [x] NSM có unit of value, quality threshold, frequency.
- [x] Có leading indicators và counter-metrics.
- [x] Loop có ít nhất 2 chu kỳ.
- [x] Tracking có 8 events, mọi event map về metric.
- [x] Có ≥2 acceptance criteria.
- [ ] **Học viên đã tự xác nhận/rewrite core action.**
- [ ] **Học viên đã tự xác nhận/rewrite cadence conclusion.**
- [ ] **Học viên đã tự xác nhận/rewrite metric hypothesis.**
- [ ] **Học viên đã tự viết rationale/reflection cá nhân.**
