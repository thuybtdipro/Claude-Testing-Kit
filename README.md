# Claude Testing Kit — Hướng Dẫn Sử Dụng

Bộ công cụ AI-assisted manual testing tích hợp với Claude Code. Giúp QC sinh test cases chất lượng cao, phân tích requirements và sinh test data — ngay trong VSCode.

---

## 1. Yêu cầu

- **VSCode** đã cài sẵn
- **Tài khoản Claude** (claude.ai) — plan Pro hoặc Max

---

## 2. Cài đặt Claude Code

1. Mở VSCode → vào **Extensions** (`Cmd+Shift+X`)
2. Tìm **"Claude Code"** → Install
3. Mở terminal trong VSCode (`Ctrl+`` ` ``) → chạy:
   ```
   claude
   ```
4. Làm theo hướng dẫn đăng nhập tài khoản Claude hiện ra trong terminal
5. Sau khi đăng nhập, gõ `/help` để kiểm tra hoạt động

---

## 3. Setup cho dự án mới

Mỗi lần bắt đầu dự án mới, thực hiện **1 lần duy nhất**:

### Bước 1 — Copy kit vào project

Copy 2 folder sau từ `Claude_Testing_Kit` vào **root folder của dự án**:

```
.claude/
```

### Bước 2 — Tạo cấu trúc folder dự án

```
my-project/
├── .claude/
│   ├── commands/        ← copy từ kit
│   └── skills/          ← copy từ kit
├── CLAUDE.md            ← tạo mới (xem mẫu bên dưới)
├── requirements/        ← lưu spec, design, Q&A
│   ├── module-01/
│   │   ├── spec.md
│   │   ├── qa-notes.md
│   │   └── ...
│   ├── module-02/
│   │   ├── spec.md
│   │   ├── qa-notes.md
│   │   └── ...
│   └── ...
└── test-cases/          ← lưu output test cases
    ├── module-01/
    │   ├── tc_submodule_01.md
    │   ├── tc_submodule_02.md
    │   └── ...
    ├── module-02/
    │   └── ...
    └── ...
```

### Bước 3 — Tạo `CLAUDE.md`

Tạo file `CLAUDE.md` ở root dự án. File này là "brain brief" cho AI — **được load đầu tiên mỗi session** trước khi AI đọc bất kỳ file nào khác. Giữ dưới 50 dòng là best practice; trên 100 dòng sẽ chiếm context window trước khi AI đọc spec.

```markdown
# [Tên Dự Án]
Mô tả: [Hệ thống làm gì, đối tượng dùng là ai]

## Modules                          ← optional, thêm khi có nhiều module
- **[Module A]** — [mô tả ngắn chức năng]
- **[Module B]** — [mô tả ngắn chức năng]

## Business Rules quan trọng        ← optional, chỉ ghi rule AI dễ assume sai
- [Ví dụ: "Sub-category không thể active nếu parent Category đang inactive"]

## Quy ước                          ← optional
- Output language: Tiếng Việt
- [Convention riêng nếu có]
```

Nội dung tối thiểu bắt buộc chỉ là 2 dòng đầu — các section còn lại thêm khi cần. Business rules không cần liệt kê hết — AI sẽ khai thác dần qua Bước 2 Q&A trong FULL RBT.

**Không cần đưa vào CLAUDE.md:**
- Nội dung spec đầy đủ → để trong `requirements/`
- Danh sách commands → commands tự load skill khi chạy
- Hướng dẫn sử dụng kit → đã có trong README này

### Bước 4 — Mở đúng folder trong VSCode

Mở **root folder của dự án** trong VSCode (không phải subfolder). Slash commands chỉ hoạt động khi `.claude/commands/` nằm ở root của workspace đang mở.

---

## 4. Cách dùng Slash Commands

Mỗi slash command (`/tên_command`) tương ứng với 1 workflow — gõ `/` trong chat Claude Code để xem danh sách.

### Tips khi dùng

**Cung cấp context trước, gõ slash command ở dòng cuối** — tránh AI hỏi lại nhiều lượt:

```
Dự án: [Tên dự án]
Module: [Tên module]
---
[paste nội dung spec hoặc attach file]
---
/slash_command
```

Nếu file đã có trong project folder: dùng `@filename` để attach thay vì paste.

**Lưu ý với FULL RBT:** front-load context giúp bỏ qua bước "cung cấp file đi" — nhưng Bước 2 (Q&A) và Bước 4 (review scenarios) vẫn dừng chờ input. Đây là checkpoint có chủ ý, không phải lỗi.

**Kết hợp `/analyze_requirement_document` → `/generate_manual_testcases_rbt` để rút ngắn Bước 2:**
1. Chạy `/analyze_requirement_document` với spec + AC từ PM → nhận AC đã cross-check + gợi ý bổ sung từ góc nhìn QC
2. Confirm với PM nếu cần → lưu AC đã chốt vào `requirements/module-xx/qa-notes.md`
3. Chạy `/generate_manual_testcases_rbt`, paste AC + Q&A đã chốt vào context:

```
Dự án: [Tên dự án]
Module: [Tên module]
---
[spec]
AC + Q&A đã chốt: [paste từ qa-notes.md]
---
/generate_manual_testcases_rbt
```

→ Bước 2 chỉ xác định luồng + hỏi thêm nếu còn thiếu, không phân tích Ambiguities lại từ đầu.

**Chọn model phù hợp với workflow:**

| Model | Workflow phù hợp |
|-------|----------------|
| **Opus** | `/generate_cross_module_test_plan` — phức tạp nhất, cần reasoning sâu |
| **Sonnet** | `/generate_manual_testcases_rbt` · `/analyze_requirement_document` · `/update_testcases_from_requirements` · `/generate_regression_suite` · `/generate_qc_onboarding_report` |
| **Haiku** | `/generate_bug_report` · `/generate_test_data` · `/generate_test_execution_checklist` — tác vụ đơn giản, format-heavy |

Chỉ định model khi mở session: `claude --model claude-sonnet-4-6`

**CLAUDE.md nên ngắn gọn:** file này được load vào mỗi session — nếu quá dài sẽ chiếm context window trước khi AI đọc spec. Chỉ ghi những gì AI cần biết về hệ thống.

**Spec quá dài → chia module trước khi paste:** nếu spec > 5000 từ, paste cả file dễ làm AI mất focus. Chia theo module, chạy từng phần — output tốt hơn.

### Sinh & Cập nhật Test Cases
| Command | Khi nào dùng |
|---------|-------------|
| `/generate_manual_testcases_rbt` | Sinh test cases theo quy trình **FULL RBT 6 bước** — dùng cho module phức tạp |
| `/generate_testcases_from_requirements` | Sinh test cases **nhanh** từ requirements đã rõ ràng — scope nhỏ, 1 lượt |
| `/update_testcases_from_requirements` | Cập nhật bộ TC khi requirements thay đổi — phân tích delta, không viết lại từ đầu |

### Phân tích & Lập kế hoạch
| Command | Khi nào dùng |
|---------|-------------|
| `/analyze_requirement_document` | Phân tích Backlog ticket / doc — phát hiện ambiguities, sinh checklist AC. **Không sinh TC** |
| `/generate_cross_module_test_plan` | Phân tích luồng đa module, sinh ma trận kết hợp (Pairwise) |
| `/generate_requirements_from_website` | Truy cập URL → tự động phân tích UI và sinh requirements document |

### Thực thi & Theo dõi
| Command | Khi nào dùng |
|---------|-------------|
| `/generate_test_execution_checklist` | Sinh checklist thực thi có thứ tự ưu tiên trước release |
| `/generate_regression_suite` | Xác định TCs cần chạy lại khi có code change |
| `/generate_exploratory_charter` | Sinh charter cho exploratory testing có cấu trúc |
| `/generate_qc_onboarding_report` | Tổng hợp hiện trạng dự án cho QC mới join giữa chừng |

### Test Data & Bug
| Command | Khi nào dùng |
|---------|-------------|
| `/generate_test_data` | Sinh test data có cấu trúc (positive, negative, boundary, edge cases) |
| `/generate_bug_report` | Chuẩn hóa bug report sẵn sàng paste vào Backlog |

---

## 5. Workflows thường dùng

### Sinh test cases cho module mới (chuẩn)

```
/generate_manual_testcases_rbt
```

**Quy trình 6 bước — agent sẽ dừng chờ bạn tại 2 điểm:**
1. Cung cấp requirements (spec, Q&A, design link)
2. Bước 2: Trả lời Q&A từ agent
3. Bước 4: Review danh sách scenarios trước khi sinh TC chi tiết
4. Output: Bảng test cases Markdown sẵn sàng copy sang Excel / Backlog

---

### Khi requirements thay đổi giữa chừng

```
/update_testcases_from_requirements
```

Cung cấp bộ TC hiện có + mô tả thay đổi → agent phân tích delta và chỉ cập nhật những TCs bị ảnh hưởng.

---

### Trước khi release

```
/generate_test_execution_checklist
```

Cung cấp bộ TC + loại release (hotfix/sprint/major) → nhận checklist có thứ tự ưu tiên + ước lượng thời gian.

---

### Sau khi có code change

```
/generate_regression_suite
```

Cung cấp bộ TC + danh sách thay đổi → nhận danh sách TCs cần chạy lại (không cần chạy full).

---

### Khi tìm được bug

```
/generate_bug_report
```

Mô tả lỗi bằng ngôn ngữ tự nhiên → nhận bug report chuẩn sẵn sàng paste vào Backlog.

---

### Phân tích ticket trước khi viết test

```
/analyze_requirement_document
```

Cung cấp Backlog ticket hoặc file spec → nhận danh sách ambiguities + AC checklist.

---

### Sinh test cases nhanh (scope nhỏ)

```
/generate_testcases_from_requirements
```

Cung cấp requirements → agent sinh TC ngay trong 1 lượt, không cần qua 6 bước.

---

## 6. Lưu ý quan trọng

- Tất cả output bằng **Tiếng Việt**
- Test data trong TCs phải **cụ thể** — agent sẽ không dùng placeholder chung chung
- Mỗi input field có **validation TC riêng** — không gộp nhiều fields vào 1 TC
- Với FULL RBT: **không bỏ qua bước nào**, đặc biệt Bước 2 (Q&A với user) là điểm quan trọng nhất

---

## 7. Lộ trình triển khai cho team

Không cần dùng tất cả workflows ngay từ đầu. Làm quen theo từng giai đoạn:

### Giai đoạn 1 — Tuần 1-2: Xây nền tảng

| Workflow | Mục đích |
|----------|---------|
| `/analyze_requirement_document` | Dùng **trước** khi viết TC — bắt ambiguity sớm, tránh viết TC sai |
| `/generate_testcases_from_requirements` | Sinh TC nhanh cho scope nhỏ, requirements đã rõ — 1 lượt, không checkpoint |
| `/generate_manual_testcases_rbt` | Core của kit — dùng cho module phức tạp, cả team phải thành thạo |
| `/update_testcases_from_requirements` | Dùng ngay khi spec thay đổi — spec thay đổi từ tuần 2 là bình thường |

**Mục tiêu:** Có bộ TC đầu tiên + team biết cập nhật TC khi spec thay đổi mà không viết lại từ đầu.

### Giai đoạn 2 — Tuần 3-4: Quản lý & vận hành sprint

| Workflow | Mục đích |
|----------|---------|
| `/generate_test_execution_checklist` | Áp dụng trước sprint release đầu tiên |
| `/generate_regression_suite` | Áp dụng từ sprint thứ 2 khi đã có TC library |

**Mục tiêu:** Vận hành được một sprint đầy đủ với sự hỗ trợ của kit.

### Để sau — khi team đã quen

`/generate_cross_module_test_plan` · `/generate_exploratory_charter` · `/generate_test_data`

---

> **Nguyên tắc:** Mỗi sprint giới thiệu thêm 1-2 workflow mới. Workflow đầu tiên team dùng thành thạo sẽ tạo trust — adoption các workflow sau sẽ dễ hơn nhiều.

### Dùng theo sự kiện — không theo sprint

| Workflow | Khi nào kích hoạt |
|----------|------------------|
| `/generate_qc_onboarding_report` | Mỗi khi có QC mới join dự án giữa chừng hoặc QC cũ quay lại sau thời gian dài |
| `/generate_bug_report` | Mỗi khi tìm được bug cần report — không cần prerequisite, dùng được ngay từ ngày đầu |

> Các workflow này không nằm trong vòng lặp sprint — dùng ngay khi có sự kiện, bất kể đang ở giai đoạn nào.
