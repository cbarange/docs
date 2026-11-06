# Guardrail

**Guardrail** giúp bạn bảo vệ dữ liệu và kiểm soát nội dung trước khi request được gửi tới model AI mà không cần sửa đổi bất kỳ dòng code nào ở phía client. Sau khi gắn Guardrail vào các model đang bật trên Model as a Service (MaaS), mọi request gọi các model đó đều tự động đi qua ba lớp kiểm tra: chặn theo từ khoá, đánh giá nội dung bằng model "giám sát", và làm sạch thông tin nhạy cảm.

Vị trí trên Console: **Protect & Govern → Guardrail** (trang danh sách) và **Configuration → Guardrail** (tạo mới).

***

## Guardrail hoạt động như thế nào?

Guardrail được triển khai ngay tại gateway của MaaS. Client vẫn gọi endpoint MaaS như bình thường — request đi qua Guardrail theo thứ tự: **Word filters** → **Prompt-based filter** → **Sensitive information**, rồi mới tới model.

<figure><img src="../../../.gitbook/assets/guardrail-concept-light.png" alt=""><figcaption></figcaption></figure>

Word filters và Prompt-based filter có thể **từ chối** request (HTTP 400). Sensitive information không chặn — nó che/thay/xoá dữ liệu nhạy cảm trước khi request tới model.

***

## Lợi ích

| Lợi ích                            | Mô tả                                                                                                                                      |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Chặn nội dung độc hại              | Từ khoá cấm, prompt vượt rào (jailbreak), yêu cầu giả danh quản trị… bị từ chối trước khi tới model.                                       |
| Che dữ liệu nhạy cảm               | Email, số thẻ tín dụng, số điện thoại, mã IBAN… được che/thay thế/xoá tự động — dữ liệu không rời khỏi hệ thống của bạn ở dạng nguyên bản. |
| Không phải sửa code                | Gắn Guardrail vào model là mọi request qua MaaS được bảo vệ, client vẫn gọi API như bình thường.                                           |
| Bảo vệ cả system prompt (tuỳ chọn) | Mặc định system prompt không bị quét; dùng tag `<vng-guard-content>` nếu muốn quét một phần.                                               |
| Một cấu hình, nhiều model          | Một Guardrail gắn được vào nhiều model MaaS của bạn.                                                                                       |

***

## Ba lớp kiểm tra

| Bộ lọc                    | Làm gì                                                                 | Khi nào áp dụng                                                 |
| ------------------------- | ---------------------------------------------------------------------- | --------------------------------------------------------------- |
| **Word filters**          | So khớp chính xác từ/cụm từ bạn khai báo                               | Luôn chạy trước, nhanh và rẻ                                    |
| **Prompt-based filter**   | Một LLM "giám sát" đọc toàn bộ nội dung và đánh giá theo mô tả của bạn | Sau word filter; hiểu ngữ cảnh nên bắt được cả câu diễn đạt lại |
| **Sensitive information** | Nhận diện thực thể PII (cấu hình sẵn) và regex tuỳ chỉnh               | Chạy cuối; **sửa lại request** thay vì chặn                     |

### Word filters

So khớp chính xác từ hoặc cụm từ bạn khai báo. Lớp này luôn chạy đầu tiên vì nhanh và rẻ — phù hợp với danh sách từ cấm cụ thể (từ nhạy cảm thương hiệu, mã nội bộ, từ tục tĩu…).

### Prompt-based filter

Một model "giám sát" (judge) đọc toàn bộ nội dung request và đánh giá theo các mô tả bạn định nghĩa, ví dụ _"yêu cầu bỏ qua hướng dẫn hệ thống"_. Vì hiểu ngữ cảnh, lớp này bắt được cả nội dung được diễn đạt lại — thứ word filter không thấy được.

### Sensitive information

Nhận diện các thực thể PII có sẵn (email, số thẻ tín dụng, số điện thoại, mã IBAN…) và regex tuỳ chỉnh của bạn. Khác với hai lớp trên, lớp này không từ chối request — nó che/thay/xoá dữ liệu nhạy cảm để request tới model đã an toàn.

### Hành động của từng bộ lọc

Mỗi rule bạn khai báo kèm một hành động:

| Bộ lọc                    | Hành động   | Ý nghĩa                                                               |
| ------------------------- | ----------- | --------------------------------------------------------------------- |
| **Word filters**          | **Mask**    | Che từ bị phát hiện trước khi gửi đi                                  |
|                           | **Detect**  | Chỉ ghi nhận/log, request vẫn đi tiếp                                 |
|                           | **Block**   | Từ chối request (trả lỗi 400)                                         |
| **Prompt-based filter**   | **Detect**  | Chỉ ghi nhận/log                                                      |
|                           | **Block**   | Từ chối request (trả lỗi 400)                                         |
| **Sensitive information** | **Mask**    | Che một phần ký tự — ví dụ `abc@gmail.com` → `***@gmail.com`          |
|                           | **Replace** | Thay bằng tên loại phát hiện được — ví dụ `abc@gmail.com` → `<EMAIL>` |
|                           | **Delete**  | Xoá hẳn thông tin đó khỏi nội dung                                    |

{% hint style="info" %}
Sensitive information không có hành động Block — mục tiêu của nó là biến dữ liệu nhạy cảm thành an toàn rồi mới gửi đi, không phải từ chối request.
{% endhint %}

***

## Phạm vi quét và system prompt

Guardrail mặc định quét **nội dung hội thoại** (các vai trò user, assistant…) trong request. Phần **system prompt** (lời chỉ dẫn cấu hình cho model) mặc định **không bị quét** — vì nó thường chứa rule nghiệp vụ của chính bạn, quét sai dễ làm hỏng hoạt động của assistant.

Nếu bạn muốn một phần system prompt được quét, chèn tag `<vng-guard-content>` vào đúng vị trí bắt đầu phần cần quét — chi tiết xem ở [Quét system prompt với tag `<vng-guard-content>`](quet-system-prompt-voi-tag-vng-guard-content.md).

{% hint style="info" %}
Tag chỉ có tác dụng **mở rộng** phạm vi quét — nó không thể dùng để "tắt" quét phần nào đang được kiểm tra. Ai chèn thêm tag vào nội dung của mình cũng chỉ làm phần đó bị quét kỹ hơn.
{% endhint %}

***

## Trạng thái Guardrail

| Trạng thái                   | Ý nghĩa                                             |
| ---------------------------- | --------------------------------------------------- |
| **ACTIVE**                   | Guardrail đã sẵn sàng, đang bảo vệ các model đã gắn |
| **CREATING**                 | Đang được cấp phát trên gateway                     |
| **UPDATING**                 | Đang áp dụng thay đổi cấu hình                      |
| **ATTACH\_FAILED**           | Lần gắn model gần nhất lỗi — có thể sửa và gắn lại  |
| **CREATE\_ERROR / ERROR**    | Tạo hoặc áp dụng cấu hình lỗi                       |
| **DELETING / DELETE\_ERROR** | Đang xoá / xoá lỗi                                  |

{% hint style="info" %}
Trạng thái chỉ phản ánh việc cấu hình đã được "nối dây" xuống gateway hay chưa. Chỉ ở trạng thái **ACTIVE** hoặc **ATTACH\_FAILED** bạn mới gắn/sửa được model.
{% endhint %}

***

## Bắt đầu với Guardrail

| Tôi muốn...                                              | Đi đến                                    |
| -------------------------------------------------------- | ----------------------------------------- |
| Tạo Guardrail đầu tiên và gắn vào model                  | [Sử dụng Guardrail](su-dung-guardrail.md) |
| Gọi API qua Guardrail, xem request bị chặn trông thế nào | [Sử dụng Guardrail](su-dung-guardrail.md) |
| Quét phần nội dung động trong system prompt | [Quét system prompt với tag `<vng-guard-content>`](quet-system-prompt-voi-tag-vng-guard-content.md) |
| Tra cứu giới hạn, thông báo lỗi và FAQ                   | [Tham chiếu và FAQ](tham-chieu-va-faq.md) |
