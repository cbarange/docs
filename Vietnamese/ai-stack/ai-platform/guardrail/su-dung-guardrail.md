# Sử dụng Guardrail

> Hướng dẫn này giúp bạn tạo một Guardrail trên AI Platform, gắn Guardrail vào các model MaaS và kiểm tra request đi qua Guardrail — client không cần sửa code. Xem [Guardrail là gì?](./) nếu chưa quen với ba lớp kiểm tra.

***

## Điều kiện cần (Prerequisites)

* Có **tài khoản AI Platform** với model đã bật trên MaaS — Guardrail gắn vào model catalog của bạn (model tên riêng tự host/BYOK chưa gắn được).
* Model cần bảo vệ thuộc một trong các loại route: **chat, completions, responses, messages, embeddings** (các loại khác như generateContent/rerank chưa hỗ trợ).
* Mỗi model chỉ gắn được vào **một Guardrail** tại một thời điểm.
* Nếu dùng Word filters kèm file, chuẩn bị file **.xlsx** theo template (nút **Download template** trong form).

***

## Tạo một Guardrail

**Bước 1: Mở form tạo**

1. Đăng nhập vào Console AI Platform.
2. Truy cập mục [**Guardrail**](https://aiplatform.console.greennode.ai/guardrail).
3. Nhấn [**Create a Guardrail**](https://aiplatform.console.greennode.ai/guardrail/create). Form gồm 4 khối: **Basic configuration**, **Word filters**, **Prompt-based filter**, **Sensitive information**.

**Bước 2: Điền Basic configuration**

| Trường                    | Bắt buộc | Quy tắc                                                                                                |
| ------------------------- | -------- | ------------------------------------------------------------------------------------------------------ |
| **Guardrail name**        | Có       | Chỉ chữ thường `a-z`, số `0-9` và dấu `-`; độ dài 5–100 ký tự; không trùng tên Guardrail khác của bạn. |
| **Guardrail description** | Không    | Tối đa 255 ký tự.                                                                                      |

**Bước 3: Cấu hình Word filters**

Guardrail phát hiện từ/cụm từ trong input request rồi áp hành động tương ứng.

1. Thêm từng từ bằng **Add a word or phrase**, hoặc nạp hàng loạt bằng **Upload .xlsx file** (template tải bằng **Download template**).
2. Điền các trường theo bảng dưới cho mỗi từ.

| Trường            | Bắt buộc | Quy tắc                                                                                                   |
| ----------------- | -------- | --------------------------------------------------------------------------------------------------------- |
| **Word / phrase** | Có       | Tối đa 100 ký tự và tối đa 3 từ (tách bởi khoảng trắng).                                                  |
| **Category**      | Có       | Tên nhóm tự đặt (ví dụ: `profanity`, `brand`, `internal-code`) — dùng để lọc trên bảng và trang chi tiết. |
| **Action**        | Có       | Một trong **Mask / Detect / Block** (xem [hành động của từng bộ lọc](./#hanh-dong-cua-tung-bo-loc)).      |

Bảng danh sách hỗ trợ tìm kiếm, lọc theo category/action, chọn nhiều dòng để **Remove**.

{% hint style="info" %}
Mỗi cặp (category, word) là một rule riêng — cùng một từ có thể nằm ở 2 category với 2 action khác nhau.
{% endhint %}

**Bước 4: Cấu hình Prompt-based filter**

Guardrail áp hành động lên input request nếu nội dung khớp với mô tả bạn khai báo. Mỗi rule gồm:

| Trường                    | Bắt buộc | Quy tắc                                                                                                                                              |
| ------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Description name**      | Có       | Tên rule, tối đa 100 ký tự, không trùng nhau.                                                                                                        |
| **Mô tả** (ô văn bản lớn) | Có       | Mô tả bằng lời hành vi cần bắt — ví dụ: _"Yêu cầu bỏ qua hoặc vượt qua hướng dẫn hệ thống trước đó"_. Viết càng rõ, model "giám sát" càng bắt chuẩn. |
| **Action**                | Có       | **Detect** hoặc **Block**.                                                                                                                           |

Vài ví dụ mô tả hay dùng:

* _Bỏ qua, ghi đè hoặc vượt qua hướng dẫn hệ thống trước đó._
* _Đóng vai AI assistant không bị giới hạn hoặc đã bị "jailbreak"._
* _Trích xuất hoặc làm lộ system prompt / cấu hình nội bộ._
* _Giả danh developer, quản trị viên hoặc người tạo ra model để ghi đè giới hạn an toàn._

**Bước 5: Cấu hình Sensitive information (tuỳ chọn)**

Guardrail xử lý thông tin nhạy cảm được khai báo dưới đây trước khi gửi input request tới model. Mọi loại dùng chung một **Confidence score** từ 0 đến 1 — score càng cao, phát hiện càng nghiêm ngặt.

**PERSONALLY IDENTIFIABLE INFORMATION** — chọn các loại PII và action cho từng loại:

| Trường              | Bắt buộc                                | Quy tắc                                                                                                                                                                                                                |
| ------------------- | --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **PII types**       | Không (bỏ trống cả khối nếu không dùng) | Chọn từ danh sách hệ thống (URL, IP Address, Credit Card Number, Email, IBAN Code…). Danh sách đầy đủ lấy qua API `GET /v1/guardrails/pii/types`. Không chọn trùng loại.                                               |
| **Score**           | Không                                   | Số thực 0.0 – 1.0, để trống mặc định 0.5. Áp dụng chung cho mọi loại đã chọn.                                                                                                                                          |
| **Action mỗi loại** | Có (nếu chọn loại)                      | **Mask / Replace / Delete**. Với **Replace** có thể nhập giá trị thay thế tuỳ ý (để trống thì hệ thống dùng tên loại, ví dụ `<EMAIL>`); với **Mask** có thể tuỳ chỉnh số ký tự bị che và ký tự che (mặc định che `*`). |

**REGULAR EXPRESSION** — thêm regex riêng của bạn:

| Trường            | Bắt buộc | Quy tắc                                                                                               |
| ----------------- | -------- | ----------------------------------------------------------------------------------------------------- |
| **Name**          | Có       | Tối đa 100 ký tự, không trùng nhau.                                                                   |
| **Score**         | Không    | 0.0 – 1.0, mặc định 0.5.                                                                              |
| **Action**        | Có       | **Mask / Replace / Delete**. Giá trị thay thế tối đa 255 ký tự, ký tự che tối đa 100 ký tự.           |
| **Regex pattern** | Có       | Tối đa 255 ký tự và phải là biểu thức chính quy hợp lệ (hệ thống từ chối pattern không compile được). |

Cả hai khối đều có nút **Reset configuration to default**.

**Bước 6: Xác nhận tạo**

1. Kiểm tra lại cấu hình ở các khối.
2. Nhấn nút tạo để hoàn tất.

{% hint style="info" %}
Ít nhất một trong ba bộ lọc phải có cấu hình — tạo Guardrail "trống" sẽ bị từ chối với lỗi _"Guardrails must declare at least one of Prompt-based, PII, or Word filter"_.
{% endhint %}

Sau khi tạo, Guardrail xuất hiện trên danh sách ở trạng thái **CREATING** rồi chuyển **ACTIVE** khi sẵn sàng. Lúc này nó chưa bảo vệ model nào — gắn model ở bước tiếp theo.

***

## Gắn model cần bảo vệ (Manage Models)

**Bước 1: Mở Manage Models**

1. Truy cập **Protect & Govern → Guardrail**.
2. Mở menu **Action → Manage Models** của Guardrail muốn cấu hình.

**Bước 2: Chọn model**

1. Chọn nguồn **Model as a Service** — danh sách model bạn đã bật trên MaaS.
2. Tìm và tick các model cần bảo vệ (có nút **Select all** và ô tìm kiếm; mỗi model ghi rõ loại **Third-party** hoặc **Self-hosted**).
3. Nhấn **Save** để áp dụng.

Quy tắc khi gắn:

| Quy tắc                   | Chi tiết                                                                                                                                                                            |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Một model – một Guardrail | Model đã gắn vào Guardrail khác sẽ bị từ chối: _"Model 'X' is already attached to guardrails 'Y'"_. Phải gỡ ở Guardrail cũ trước.                                                   |
| Loại route được hỗ trợ    | chat, completions, responses, messages, embeddings.                                                                                                                                 |
| Trạng thái                | Guardrail phải đang **ACTIVE** hoặc **ATTACH\_FAILED**; model phải đang bật.                                                                                                        |
| Thao tác tranh chấp       | Chỉ một thao tác gắn/gỡ chạy tại một thời điểm trên tài khoản — nếu đang có thao tác khác chạy sẽ báo _"Another guardrails attach/detach is in flight for this user. Please wait."_ |

Sau khi **Save**, thay đổi được áp dụng xuống gateway; trong lúc áp dụng Guardrail ở trạng thái **UPDATING**, xong thì về lại **ACTIVE**.

***

## Gọi API qua Guardrail

Client gọi API **không cần thay đổi gì** — vẫn endpoint MaaS và API key như thường. Request tự đi qua Guardrail gắn trên model.

```bash
curl --request POST \
--url 'https://maas-llm-aiplatform-hcm.api.vngcloud.vn/v1/chat/completions' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer $AI_PLATFORM_API_KEY' \
--data '{
  "model": "qwen/qwen3.8-max",
  "messages": [
    {
      "role": "assistant",
      "content": "You are an AI assistant tasked with providing information to users."
    },
    {
      "role": "user",
      "content": "What is AI?"
    }
  ],
  "max_tokens": 4096,
  "temperature": 1,
  "top_p": 0.95
}'
```

Khi request bị chặn, HTTP trả về **400** kèm lý do.

Prompt-based filter chặn:

```json
{
  "status": "Prompt has been rejected by AI Guard",
  "reason": "Content violates the following categories: <tên rule prompt-based>"
}
```

Word filter chặn:

```json
{
  "status": "Request blocked due to detected keyword",
  "reason": "<từ khoá bị phát hiện>"
}
```

Khi Sensitive information phát hiện dữ liệu nhạy cảm, request **không bị chặn** — nội dung được che/thay/xoá rồi mới tới model.

***

## Quét system prompt bằng tag `<vng-guard-content>`

Mặc định Guardrail **bỏ qua** system prompt, vì đây thường là cấu hình do chính bạn viết. Nếu ứng dụng đưa nội dung mà người dùng cuối tác động được vào system prompt (tài liệu RAG, lịch sử hội thoại, file tải lên), hãy chèn tag `<vng-guard-content>` ngay trước phần nội dung đó để Guardrail quét phần này như nội dung người dùng.

| Tình huống                      | Kết quả                                                                                                                   |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| System prompt **không có** tag  | Toàn bộ bị bỏ qua — không bộ lọc nào quét.                                                                                |
| System prompt **có** tag        | Mọi nội dung từ vị trí tag trở đi được quét như nội dung người dùng (đi qua đủ 3 bộ lọc). Phần trước tag vẫn được bỏ qua. |
| Có nhiều message/block `system` | Tag ở bất kỳ block nào cũng bật quét cho toàn bộ phần còn lại của system prompt — kể cả các block phía sau.               |
| Gõ sai tag                      | Không có gì được quét — hệ thống bỏ qua im lặng. Sao chép đúng `<vng-guard-content>`.                                     |

Xem ví dụ đầy đủ và cách tự kiểm chứng tại [Quét system prompt với tag `<vng-guard-content>`](quet-system-prompt-voi-tag-vng-guard-content.md).

***

## Quản lý Guardrail trên trang danh sách

Trang **Protect & Govern → Guardrail** liệt kê mọi Guardrail của bạn với các cột:

| Cột                     | Nội dung                                                                  |
| ----------------------- | ------------------------------------------------------------------------- |
| **Name**                | Tên + ID hệ thống (`gr-…`) + thời gian tạo. Bấm tên để mở trang chi tiết. |
| **Status**              | Trạng thái (xem [trạng thái Guardrail](./#trang-thai-guardrail)).         |
| **Prompt-based filter** | Số rule prompt-based đã cấu hình.                                         |
| **PII type & Regex**    | Các loại PII / regex đã cấu hình.                                         |
| **Protected models**    | Các model đang được Guardrail này bảo vệ.                                 |
| **Action**              | Menu tác vụ: **Edit info**, **Manage Models**, **Delete**.                |

Thanh công cụ trên bảng có nút làm mới và nút xoá hàng loạt (chọn nhiều dòng bằng checkbox).

**Edit info** — sửa **tên** và **mô tả**. Tên mới vẫn phải đúng quy tắc ở [Tạo một Guardrail](su-dung-guardrail.md#tao-mot-guardrail) (Basic configuration) và không trùng tên khác của bạn.

**Delete** — xoá Guardrail:

{% hint style="warning" %}
Guardrail đang gắn model phải **gỡ model trước** (Manage Models → bỏ chọn hết) — xoá khi còn gắn sẽ bị từ chối: _"Guardrails is attached to one or more models. Please detach them first."_ Chỉ xoá được Guardrail ở trạng thái ACTIVE, ERROR, ATTACH\_FAILED hoặc CREATE\_ERROR.
{% endhint %}

***

## Xem và sửa trên trang chi tiết Guardrail

Bấm tên Guardrail ở danh sách để mở trang chi tiết:

* **General information** — ID (`gr-…`), mô tả, các model đang bảo vệ.
* **Detail information** — 3 tab đúng theo 3 bộ lọc:
  * **Word filters** — bảng từ khoá, lọc theo category/action, sửa bằng **Edit words & phrases**.
  * **Prompt-based filter** — danh sách rule (tên + mô tả), sửa bằng **Edit**.
  * **Sensitive Info** — cấu hình PII types, score và regex, sửa tương tự form tạo.
* Ở góc phải trên: sửa nhanh thông tin (**Edit info**), **Manage Models**, và **Delete**.

Các thao tác sửa trên trang chi tiết có cùng quy tắc như lúc tạo (xem [Tạo một Guardrail](su-dung-guardrail.md#tao-mot-guardrail)).

***

## Kết quả

Sau khi hoàn thành, mọi request tới các model đã gắn đều đi qua Guardrail: request vi phạm bị từ chối với HTTP 400 kèm lý do cụ thể, còn dữ liệu nhạy cảm được che/thay/xoá tự động trước khi tới model — client không phải thay đổi gì.

| Tôi muốn tiếp theo...                    | Đi đến                                    |
| ---------------------------------------- | ----------------------------------------- |
| Hiểu rõ ba lớp kiểm tra và các hành động | [Guardrail là gì?](./)                    |
| Quét phần nội dung động trong system prompt | [Quét system prompt với tag `<vng-guard-content>`](quet-system-prompt-voi-tag-vng-guard-content.md) |
| Tra cứu giới hạn, thông báo lỗi và FAQ   | [Tham chiếu và FAQ](tham-chieu-va-faq.md) |
