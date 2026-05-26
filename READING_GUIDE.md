# Hướng Dẫn Đọc Kit — Trước Buổi Workshop

Đọc theo thứ tự dưới đây.

> **Lưu ý:** Mở file này trong **VSCode** và bật Preview (`Cmd+Shift+V`) để các link dẫn đến file khác hoạt động được.

---

## Cấu trúc kit

Khi mở folder kit, bạn sẽ thấy:

| File / Folder | Nội dung |
|--------------|---------|
| `README.md` | Hướng dẫn sử dụng toàn bộ kit |
| `CLAUDE.md` | Brief cho AI — đọc tự động mỗi session, không cần đụng vào |
| `.claude/commands/` | Các slash commands — mỗi command tương ứng 1 workflow gồm 1 hoặc nhiều tác vụ QC dùng trong quá trình test (phân tích requirement, sinh TC, tạo bug report...) |
| `.claude/skills/` | Logic xử lý bên trong — AI tự đọc khi chạy command, không cần đọc thủ công |
| `sample/` | TC mẫu để tham khảo output |

---

## Bước 1 — Hiểu kit làm được gì

Mở [`README.md`](README.md), chỉ đọc các phần sau:
- **Mục 4** — Bảng Slash Commands (đọc tên + mô tả ngắn, không cần nhớ hết)
- **Mục 7, Giai đoạn 1** — 4 workflows team sẽ dùng đầu tiên

> Bỏ qua các phần còn lại trong README.

---

## Bước 2 — Xem output mẫu

Mở [`sample/tc_login_sample.md`](sample/tc_login_sample.md).

Đây là ví dụ output kit sinh ra — xem qua để biết kỳ vọng trước khi đọc hướng dẫn.

---

## Bước 3 — Đọc 1 workflow

Chọn **1 trong 3** file dưới đây để đọc:

| Bạn quan tâm hơn đến... | Đọc file này | Ghi chú |
|------------------------|-------------|---------|
| Phân tích requirement, confirm AC | [`.claude/commands/analyze_requirement_document.md`](.claude/commands/analyze_requirement_document.md) | |
| Sinh test cases nhanh (1 lượt) | [`.claude/commands/generate_testcases_from_requirements.md`](.claude/commands/generate_testcases_from_requirements.md) | |
| Sinh test cases bài bản (FULL RBT) | [`.claude/commands/generate_manual_testcases_rbt.md`](.claude/commands/generate_manual_testcases_rbt.md) | Đọc để biết flow — sẽ demo trực tiếp trong workshop |

---

## Bỏ qua hoàn toàn (sẽ cover trong workshop)

- `.claude/skills/` — các file SKILL.md dài, sẽ giải thích trực tiếp
- Các workflows còn lại — sẽ giới thiệu ở giai đoạn sau

