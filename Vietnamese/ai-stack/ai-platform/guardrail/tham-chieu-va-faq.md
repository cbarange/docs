# Tham chiếu và FAQ

Trang này tổng hợp giới hạn, thông báo lỗi thường gặp và câu hỏi thường gặp khi sử dụng **Guardrail** trên AI Platform. Hướng dẫn thao tác từng bước xem ở [Sử dụng Guardrail](su-dung-guardrail.md).

---

## Giới hạn

| Hạng mục | Giới hạn |
|---|---|
| Số Guardrail / tài khoản | Theo hạn mức hệ thống (vượt sẽ báo *"Exceeded GUARDRAILS quota."*). |
| Word filter | Tối đa 10.000 từ; mỗi từ ≤ 100 ký tự và ≤ 3 từ. |
| Prompt-based | Tối đa 10 rule; tên ≤ 100 ký tự, không trùng. |
| Regex | Tối đa 10 pattern; pattern ≤ 255 ký tự, phải hợp lệ. |
| Score | 0.0 – 1.0 cho cả PII và regex; mặc định 0.5. |
| Model / Guardrail | Mỗi model chỉ gắn vào một Guardrail tại một thời điểm. |
| System prompt | Mặc định không quét; muốn quét dùng tag `<vng-guard-content>`. |
| Model tự host / BYOK | Chưa gắn được Guardrail. |

---

## Thông báo lỗi thường gặp

### Request bị 400

| Triệu chứng | Nguyên nhân | Cách xử lý |
|---|---|---|
| `Prompt has been rejected by AI Guard` + reason nêu rule | Prompt-based filter bắt vi phạm | Xem lại nội dung gửi đi; nếu là false positive, chỉnh mô tả rule cho hẹp hơn hoặc đổi action sang Detect. |
| `Request blocked due to detected keyword` | Word filter trúng từ khoá action Block | Bỏ từ hoặc đổi action sang Detect/Mask nếu block nhầm. |
| Lỗi 400 khi tạo/sửa trên Console | Vi phạm quy tắc trường (tên sai định dạng, pattern regex hỏng, trùng tên…) | Đọc thông báo lỗi — nó nêu đúng trường vi phạm; đối chiếu quy tắc ở [Tạo một Guardrail](su-dung-guardrail.md#tao-mot-guardrail). |

### Guardrail không chặn nội dung đáng lẽ phải chặn

| Nguyên nhân | Cách xử lý |
|---|---|
| Nội dung nằm trong system prompt không có tag | Chèn `<vng-guard-content>` vào trước phần cần quét (xem [Quét system prompt](quet-system-prompt-voi-tag-vng-guard-content.md)). |
| Gõ sai tag | Sao chép đúng chuỗi `<vng-guard-content>` — sai một ký tự là không có tác dụng, không báo lỗi. |
| Rule prompt-based mô tả quá hẹp | Bổ sung mô tả rõ hơn / thêm rule cho dạng vi phạm mới. |
| Model chưa gắn Guardrail | Kiểm tra cột **Protected models**; gắn lại qua **Manage Models**. |

### Thao tác bị từ chối

| Thông báo | Nguyên nhân | Cách xử lý |
|---|---|---|
| `Guardrails is attached to one or more models. Please detach them first.` | Xoá Guardrail khi còn gắn model | Manage Models → bỏ chọn hết → Save → xoá lại. |
| `Model 'X' is already attached to guardrails 'Y'` | Model đã thuộc Guardrail khác | Gỡ model khỏi Guardrail 'Y' trước. |
| `Another guardrails attach/detach is in flight for this user. Please wait.` | Có thao tác gắn/gỡ khác đang chạy | Chờ vài giây rồi thử lại. |
| `Guardrails is <status>. Please wait for provisioning to complete and retry.` | Guardrail chưa ACTIVE | Chờ trạng thái về ACTIVE rồi thử lại. |
| `Exceeded GUARDRAILS quota.` | Vượt hạn mức số Guardrail | Xoá Guardrail không dùng hoặc liên hệ hỗ trợ nâng hạn mức. |

---

## Câu hỏi thường gặp

### Guardrail có làm chậm request không?

Có một phần. Word filter chạy nhanh; prompt-based filter phải gọi thêm một model "giám sát" nên chậm hơn. Khi nội dung sạch, chi phí này là một lượng nhỏ so với thời gian sinh câu trả lời của model chính.

### Tôi có phải sửa code client không?

Không. Gắn Guardrail vào model là xong — endpoint, header, body giữ nguyên.

### Sao tôi bị 400 "Prompt has been rejected by AI Guard"?

Nội dung request (hoặc phần system prompt sau tag `<vng-guard-content>`) bị prompt-based filter đánh giá là vi phạm. Trường `reason` cho biết rule nào bắt được — xem [Thông báo lỗi thường gặp](#thong-bao-loi-thuong-gap).

### Sensitive information "Block" được không?

Không. Bộ lọc này luôn sửa nội dung (che/thay/xoá) rồi mới gửi đi — mục tiêu là dữ liệu tới model đã an toàn.

### Nếu system prompt của tôi tình cờ chứa đúng chuỗi `<vng-guard-content>` thì sao?

Đó chính là cách tag hoạt động: phần từ vị trí đó trở đi sẽ được quét. Nếu không muốn, hãy đổi cách diễn đạt để tránh đúng chuỗi tag đó.

### Một Guardrail gắn được bao nhiêu model?

Nhiều model tùy ý (trong số model MaaS bạn đã bật), miễn mỗi model chưa gắn Guardrail khác.

### Đổi cấu hình Guardrail có cần gắn lại model không?

Không. Sửa cấu hình tự áp dụng xuống các model đang gắn (trạng thái UPDATING trong lúc áp dụng).

### Guardrail áp dụng cho cả câu trả lời của model không?

Hiện Guardrail kiểm tra input request (nội dung bạn gửi đi). Phản hồi của model chưa đi qua Guardrail.

---

## Khi nào liên hệ hỗ trợ

Nếu lỗi vẫn còn hoặc không nằm trong các mục trên, liên hệ hỗ trợ kèm:

- **Guardrail ID** (`gr-…`) và **model** bị ảnh hưởng
- **Thời điểm** xảy ra và **region**
- **HTTP status** và nội dung lỗi nhận được
