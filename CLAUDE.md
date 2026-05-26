# Claude Testing Kit — Manual QA

Bộ công cụ AI-assisted manual testing cho Claude Code. Tập trung vào sinh manual test cases chất lượng cao theo quy trình Risk-Based Testing (RBT).

---

## Skills có sẵn

| Skill | File | Mô tả |
|-------|------|-------|
| `rbt_manual_testing` | `.claude/skills/rbt_manual_testing/SKILL.md` | Master skill sinh manual test cases — 2 modes: QUICK và FULL RBT (6 bước) |
| `requirements_analyzer` | `.claude/skills/requirements_analyzer/SKILL.md` | Phân tích UI/DOM sinh requirements document |
| `bug_reporter` | `.claude/skills/bug_reporter/SKILL.md` | Chuẩn hóa bug reports — severity/priority, steps to reproduce, output sẵn sàng cho Backlog |

---

## Slash Commands có sẵn

Gọi bằng `/tên_command` trong Claude Code.

### Sinh & Cập nhật Test Cases
| Command | Khi nào dùng |
|---------|-------------|
| `/generate_manual_testcases_rbt` | Sinh test cases theo quy trình FULL RBT 6 bước — dùng cho module phức tạp |
| `/generate_testcases_from_requirements` | Sinh test cases nhanh từ requirements đã rõ ràng (QUICK mode) |
| `/update_testcases_from_requirements` | Cập nhật bộ TC hiện có khi requirements thay đổi — phân tích delta, không viết lại từ đầu |

### Phân tích & Lập kế hoạch
| Command | Khi nào dùng |
|---------|-------------|
| `/analyze_requirement_document` | Phân tích Backlog ticket / doc — extract AC từ requirements, phát hiện ambiguities — KHÔNG sinh TC |
| `/generate_cross_module_test_plan` | Phân tích luồng đa module, sinh ma trận kết hợp (Pairwise) |

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
| `/generate_test_data` | Sinh test data có cấu trúc cho forms, API |
| `/generate_bug_report` | Chuẩn hóa bug report sẵn sàng paste vào Backlog |
| `/export_to_drive` | Xuất bảng từ file markdown lên Google Sheet qua Drive MCP |

---

## Quy trình thường dùng

### Sinh test cases từ spec (module mới)
```
/generate_manual_testcases_rbt
```
→ Cung cấp requirements doc → Agent chạy 6 bước → Bảng TC Markdown

### Phân tích ticket trước khi viết test
```
/analyze_requirement_document
```
→ Cung cấp Backlog ticket / doc → Nhận danh sách ambiguities + AC checklist

### Sinh test cases nhanh (scope nhỏ)
```
/generate_testcases_from_requirements
```
→ Cung cấp requirements → Agent sinh TC ngay, 1 lượt

---

## Nguyên tắc

- Giao tiếp và output bằng **Tiếng Việt**
- Test data phải **cụ thể** — không dùng placeholder chung chung
- Mỗi input field có **validation TC riêng** — không gộp nhiều fields vào 1 TC
- FULL RBT **bắt buộc tuần tự** 6 bước — không gộp, không bỏ qua

---

## Roadmap — Backlog Integration

Tự động tạo Backlog ticket từ output của `/generate_bug_report` — thay vì copy-paste markdown thủ công.

### Điều kiện trước khi làm

- Team đã dùng `/generate_bug_report` ổn định — output quality đủ tin cậy để submit thẳng
- Có Backlog API token (lưu an toàn, không commit vào repo)

### Hướng thực hiện

1. **Build Backlog MCP server** — wrap Backlog REST API (`POST /api/v2/issues`) thành MCP tool
2. **Thêm command `/submit_bug_to_backlog`** — nhận output từ `/generate_bug_report`, map field sang Backlog issue format, gọi MCP tạo ticket
3. **Field mapping cần config theo dự án:** project ID, issue type ID, category, milestone, custom fields

### Lưu ý

- Giữ bước QC review trước khi submit — không auto-create không qua xác nhận
- API token đọc từ biến môi trường, không hardcode trong command file

---

## Roadmap — Khi mở rộng sang Automation

Kit hiện tại chỉ bao gồm **manual testing**. Khi muốn mở rộng sang automation, thực hiện theo thứ tự sau:

### Bước 1: Thêm skills và rules

Tạo các file sau trong `.claude/skills/`:
- `.claude/skills/qa_automation_engineer/SKILL.md` — Skill viết automation scripts
- `.claude/skills/ui_debug_agent/SKILL.md` — Skill inspect DOM, sinh locators
- `.claude/skills/smart_locator_agent/SKILL.md` — Skill tối ưu locator strategy

### Bước 2: Cập nhật lại các commands

Khi đã có skill `qa_automation_engineer`, cần add lại references vào các file sau:

| File | Thêm lại |
|------|---------|
| `.claude/commands/generate_application_test_plan.md` | Frontmatter `skills`: thêm `qa_automation_engineer`, `ui_debug_agent` |
| `.claude/commands/generate_cross_module_test_plan.md` | Frontmatter `skills`: thêm `qa_automation_engineer` |
| `.claude/commands/generate_test_data.md` | Frontmatter `skills`: thêm `qa_automation_engineer` |

### Bước 3: Thêm automation commands

Tạo các file trong `.claude/commands/`:
- `generate_automation_framework.md` — Bootstrap framework automation mới
- `generate_automation_from_testcases.md` — Sinh automation từ manual TCs có sẵn
- `generate_automation_from_ui_flow.md` — Sinh automation từ UI flow
- `generate_locator.md` — Sinh và heal locators
- `generate_combinatorial_test_data.md` — Sinh data cho ma trận kết hợp

### Bước 4: Cập nhật CLAUDE.md

Thêm skills và commands mới vào các bảng danh sách ở trên.
