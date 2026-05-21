# Mẫu Output TC — Module Login

> File này là **ví dụ tham khảo** cho thấy output chuẩn của kit trông như thế nào.
> Sinh bởi `/generate_manual_testcases_rbt` (FULL RBT) hoặc `/generate_testcases_from_requirements` (QUICK).

---

## Thông tin module

- **Dự án:** DEMO
- **Module:** Login
- **Sub-modules:** Form đăng nhập, Validation, Bảo mật

---

## Bảng Test Cases

| ID | Function Name | Category | Risk Level | Test Scenario | Precondition | Steps | Expected Results | Test Data | Priority |
|----|--------------|----------|-----------|--------------|-------------|-------|-----------------|-----------|---------|
| DEMO_LOGIN_TC_001 | Login | Form đăng nhập | High | [UI Visual] Verify UI tổng thể màn hình Login | Truy cập trang Login | 1. Mở trình duyệt<br>2. Truy cập URL trang Login | 1. Trang hiển thị đúng layout<br>2. Có field Email, Password, nút Đăng nhập<br>3. Không bị vỡ layout trên viewport 1280x720 | URL: /login | Medium |
| DEMO_LOGIN_TC_002 | Login | Form đăng nhập | High | [UI Visual] Verify trạng thái Normal của field Email | Trang Login đã load | 1. Quan sát field Email khi chưa tương tác | 1. Placeholder "Nhập email" hiển thị đúng<br>2. Border màu mặc định (#D1D5DB)<br>3. Label "Email" hiển thị phía trên field | — | Low |
| DEMO_LOGIN_TC_003 | Login | Form đăng nhập | High | [UI Visual] Verify trạng thái Error của field Email | Trang Login đã load | 1. Để trống field Email<br>2. Nhập password hợp lệ<br>3. Bấm nút Đăng nhập | 1. Border field Email chuyển màu đỏ<br>2. Hiển thị message "Email không được để trống" bên dưới field<br>3. Icon lỗi hiển thị (nếu có trong design) | Email: (để trống)<br>Password: Test@1234 | Medium |
| DEMO_LOGIN_TC_004 | Login | Form đăng nhập | High | Check đăng nhập thành công với email và password hợp lệ | User có tài khoản hợp lệ, chưa đăng nhập | 1. Nhập email hợp lệ vào field Email<br>2. Nhập password đúng vào field Password<br>3. Bấm nút Đăng nhập | 1. Hệ thống xác thực thành công<br>2. Redirect đến trang Dashboard<br>3. Hiển thị tên user ở header | Email: test_user_01@demo.com<br>Password: Test@1234 | Critical |
| DEMO_LOGIN_TC_005 | Login | Form đăng nhập | High | Check đăng nhập thất bại khi nhập password sai | User có tài khoản hợp lệ, chưa đăng nhập | 1. Nhập email hợp lệ vào field Email<br>2. Nhập password sai vào field Password<br>3. Bấm nút Đăng nhập | 1. Hệ thống hiển thị thông báo lỗi "Email hoặc mật khẩu không đúng"<br>2. Không redirect<br>3. Form vẫn giữ giá trị Email đã nhập | Email: test_user_01@demo.com<br>Password: WrongPass999 | Critical |
| DEMO_LOGIN_TC_006 | Login | Validation | High | Check validation field Email với format không hợp lệ | Trang Login đã load | 1. Nhập email sai format vào field Email<br>2. Nhập password hợp lệ<br>3. Bấm nút Đăng nhập | 1. Hệ thống hiển thị lỗi "Email không đúng định dạng"<br>2. Không gửi request lên server | Email: notanemail<br>Password: Test@1234 | High |
| DEMO_LOGIN_TC_007 | Login | Validation | Medium | Check validation field Password với độ dài dưới mức tối thiểu | Trang Login đã load | 1. Nhập email hợp lệ<br>2. Nhập password dưới 8 ký tự<br>3. Bấm nút Đăng nhập | 1. Hệ thống hiển thị lỗi "Mật khẩu phải từ 8 ký tự trở lên"<br>2. Không gửi request lên server | Email: test_user_01@demo.com<br>Password: Ab1@ (4 ký tự) | High |
| DEMO_LOGIN_TC_008 | Login | Bảo mật | High | Check tài khoản bị khóa sau 5 lần đăng nhập sai liên tiếp | User có tài khoản hợp lệ, chưa đăng nhập | 1. Nhập đúng email, sai password 5 lần liên tiếp<br>2. Thực hiện lần đăng nhập thứ 6 với password đúng | 1. Sau lần thứ 5: hiển thị "Tài khoản bị khóa tạm thời, vui lòng thử lại sau 15 phút"<br>2. Lần thứ 6: vẫn hiển thị thông báo khóa dù password đúng<br>3. Không cho phép đăng nhập trong thời gian khóa | Email: test_user_01@demo.com<br>Password sai: WrongPass001 → WrongPass005<br>Password đúng: Test@1234 | High |
