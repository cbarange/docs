# Chính sách mật khẩu và phiên đăng nhập

Truy cập [https://iam.console.greennode.ai/account-settings](https://iam.console.greennode.ai/account-settings) hoặc Portal -> IAM -> Account Settings. Thay đổi các thiết lập liên quan đến chính sách mật khẩu và phiên đăng nhập theo nhu cầu.

Trang **Account settings** trong **IAM** cho phép bạn thiết lập hai chính sách bảo mật áp dụng cho các tài khoản người dùng trong IAM:

* **Password policy** — các yêu cầu đối với mật khẩu: độ dài, độ phức tạp, thời hạn và việc dùng lại mật khẩu cũ.
* **Session timeout policy** — thời gian một phiên đăng nhập còn hiệu lực trước khi người dùng phải đăng nhập lại.

Mỗi chính sách có hai lựa chọn:

* **IAM default** — dùng cấu hình mặc định của GreenNode.
* **Custom** — tự cấu hình theo yêu cầu bảo mật của tổ chức.

### Password policy

#### IAM default

Đây là cấu hình được chọn sẵn. Mật khẩu phải đáp ứng các yêu cầu sau:

| Yêu cầu                     | Giá trị mặc định                                                                                   |
| --------------------------- | -------------------------------------------------------------------------------------------------- |
| **Password minimum length** | 8 ký tự                                                                                            |
| **Password strength**       | Có ít nhất 1 ký tự của mỗi loại: chữ hoa, chữ thường, chữ số và ký tự đặc biệt (non-alphanumeric). |
| **Hết hạn mật khẩu**        | Không hết hạn (_Never expire password_)                                                            |
| **Dùng lại mật khẩu cũ**    | Được phép (_Do not prevent password reuse_)                                                        |

#### Custom

Chọn **Custom** để tự đặt các yêu cầu cho mật khẩu.

<table><thead><tr><th width="234.5">Thiết lập</th><th width="325">Mô tả</th><th>Giới hạn</th></tr></thead><tbody><tr><td><strong>Password minimum length</strong></td><td>Độ dài tối thiểu của mật khẩu (số ký tự).</td><td>Từ 8 đến 50</td></tr><tr><td><strong>Require at least one uppercase letter from the Latin alphabet (A-Z)</strong></td><td>Bắt buộc có ít nhất 1 chữ hoa.</td><td>—</td></tr><tr><td><strong>Require at least one lowercase letter from the Latin alphabet (a-z)</strong></td><td>Bắt buộc có ít nhất 1 chữ thường.</td><td>—</td></tr><tr><td><strong>Require at least one number</strong></td><td>Bắt buộc có ít nhất 1 chữ số.</td><td>—</td></tr><tr><td><strong>Require at least one non-alphanumeric character</strong></td><td>Bắt buộc có ít nhất 1 ký tự đặc biệt (không phải chữ hoặc số).</td><td>—</td></tr><tr><td><strong>Turn on password expiration</strong> → <strong>Expire password in</strong></td><td>Mật khẩu hết hạn sau số ngày đã đặt, người dùng phải đổi mật khẩu mới. Mặc định 90 ngày.</td><td>Từ 1 đến 1095 ngày</td></tr><tr><td><strong>Prevent password reuse</strong> → <strong>Remember</strong></td><td>Không cho dùng lại các mật khẩu gần nhất. Mặc định nhớ 4 mật khẩu.</td><td>Từ 1 đến 24</td></tr></tbody></table>

### Session timeout policy

Thiết lập thời gian một phiên đăng nhập còn hiệu lực. Khi hết thời gian này, người dùng phải đăng nhập lại.

| Lựa chọn        | Thời gian hết hạn phiên                                                               |
| --------------- | ------------------------------------------------------------------------------------- |
| **IAM default** | 2 giờ                                                                                 |
| **Custom**      | Chọn trong danh sách **Session expires in**: 15 phút, 30 phút hoặc 1 giờ (_an hour_). |
