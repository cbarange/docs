# Dùng Codex với Minimax qua GreenNode MaaS

> Hướng dẫn cấu hình [OpenAI Codex CLI](https://github.com/openai/codex) để gọi model Minimax qua GreenNode MaaS — sử dụng Responses API thông qua custom provider `maas` định nghĩa trong `codex.toml`.

***

## Điều kiện cần (Prerequisites)

* Chuẩn bị key, Base URL và model theo [Bắt đầu với AI Coding](../bat-dau.md)
* Node.js ≥ 22 đã cài đặt

***

## Chọn cấu hình theo loại dịch vụ

Codex dùng **chuẩn OpenAI** → Base URL **có** `/v1` ở cả hai loại dịch vụ. Chỉ khác host và loại key:

| Loại dịch vụ | `base_url` | Key | `model` |
|---|---|---|---|
| **PAYG** | `https://maas-llm-aiplatform-hcm.api.vngcloud.vn/v1` | API Key từ [trang API Keys](https://aiplatform.console.greennode.ai/keys) | `minimax/minimax-m2.5` |
| **Token Plan** | `https://tokenplan.api.greennode.ai/v1` | subscription-key từ Plan Detail → tab **Subscription keys** | Model code ở tab **Models** của gói |

{% hint style="warning" %}
**Key và Base URL phải cùng một loại dịch vụ.** API Key PAYG gửi tới host `tokenplan…` (hoặc ngược lại) trả về `401 Unauthorized` dù key vẫn còn hiệu lực. Nhớ theo nơi bạn đã lấy key — xem [Mục 2 của trang Điều kiện cần](../bat-dau.md).
{% endhint %}

***

## Bước 1 — Cài đặt Codex CLI

```bash
npm install -g @openai/codex
```

Xác nhận cài thành công:

```bash
codex --version
```

***

## Bước 2 — Lấy key

{% tabs %}
{% tab title="PAYG — API Key" %}
1. Đăng nhập [AI Platform Console](https://aiplatform.console.greennode.ai/)
2. Vào **API Keys** → **Create API Key**
3. Đặt tên key (5–50 ký tự, chữ thường + số + gạch ngang)
4. Copy API key vừa tạo

{% hint style="warning" %}
API key mới tạo ở trạng thái `pending`. Đợi đến khi status = `ACTIVE` mới dùng được.
{% endhint %}
{% endtab %}

{% tab title="Token Plan — subscription-key" %}
1. Vào **API Key** → **Token Plan** → **My Token Plans**, mở gói đã mua
2. Tab **Subscription keys** → copy `default-key` hoặc key bạn tự tạo
3. Tab **Models** → copy **Model code** của model muốn dùng

Chưa có gói? Xem [Mua gói Token Plan](../../token-plan/mua-goi-token-plan.md).
{% endtab %}
{% endtabs %}

***

## Bước 3 — Cấu hình `codex.toml`

Tạo hoặc chỉnh sửa file `~/.codex/config.toml` (cấu hình toàn hệ thống) hoặc `codex.toml` tại thư mục gốc project (chỉ áp dụng cho project đó). Copy đúng tab theo loại dịch vụ của bạn:

{% tabs %}
{% tab title="PAYG" %}
```toml
# API key — export trước khi chạy Codex
# export MAAS_API_KEY="<API-key-PAYG-của-bạn>"

model_provider = "maas"
model = "minimax/minimax-m2.5"

# Cần thiết vì MAAS không trả metadata model — tránh context bị cắt sai
model_context_window = 204800
model_max_output_tokens = 16400

# MAAS backend là stateless — Codex phải gửi lại toàn bộ conversation mỗi turn
disable_response_storage = true

[model_providers.maas]
name = "MAAS"

# base_url KHÔNG có trailing /responses — Codex tự append (→ .../v1/responses)
base_url = "https://maas-llm-aiplatform-hcm.api.vngcloud.vn/v1"
env_key = "MAAS_API_KEY"
wire_api = "responses"
request_max_retries = 3
```
{% endtab %}

{% tab title="Token Plan" %}
```toml
# subscription-key — export trước khi chạy Codex
# export MAAS_API_KEY="<subscription-key-của-bạn>"

model_provider = "maas"
model = "glm-5.2"   # thay bằng Model code ở tab Models của gói

# Cần thiết vì MAAS không trả metadata model — tránh context bị cắt sai
model_context_window = 204800
model_max_output_tokens = 16400

# MAAS backend là stateless — Codex phải gửi lại toàn bộ conversation mỗi turn
disable_response_storage = true

[model_providers.maas]
name = "MAAS"

# base_url KHÔNG có trailing /responses — Codex tự append (→ .../v1/responses)
base_url = "https://tokenplan.api.greennode.ai/v1"
env_key = "MAAS_API_KEY"
wire_api = "responses"
request_max_retries = 3
```

{% hint style="info" %}
`model` phải là **Model code** đúng như tab **Models** của gói hiển thị, và model đó phải nằm trong gói — gọi model ngoài gói trả về `403 Forbidden`.
{% endhint %}
{% endtab %}
{% endtabs %}

**Giải thích các field quan trọng:**

| Field | Mục đích |
|---|---|
| `model_provider` | Key của provider trong `[model_providers.*]` |
| `model` | Model ID gửi lên MaaS — PAYG dùng Model ID của portal Models, Token Plan dùng Model code của gói |
| `model_context_window` | Khai báo thủ công vì MaaS không expose metadata model |
| `disable_response_storage` | Bắt buộc cho backend stateless — gửi lại full conversation mỗi turn |
| `base_url` | Endpoint theo loại dịch vụ, có `/v1` — Codex tự thêm `/responses` phía sau |
| `env_key` | Tên biến môi trường chứa key |
| `wire_api` | Protocol sử dụng — `responses` tương ứng OpenAI Responses API |

***

## Bước 4 — Set key và chạy Codex

Export key trong shell — dùng đúng key của loại dịch vụ đã khai trong `base_url` ở Bước 3:

{% tabs %}
{% tab title="PAYG" %}
```bash
export MAAS_API_KEY="<API-key-PAYG-của-bạn>"
```
{% endtab %}

{% tab title="Token Plan" %}
```bash
export MAAS_API_KEY="<subscription-key-của-bạn>"
```
{% endtab %}
{% endtabs %}

Để tự động mỗi lần mở terminal, thêm vào `~/.zshrc` hoặc `~/.bashrc`:

```bash
echo 'export MAAS_API_KEY="<key-của-bạn>"' >> ~/.zshrc
source ~/.zshrc
```

Chạy Codex trong thư mục project:

```bash
codex
```

Codex sẽ hiển thị provider và model đang dùng tại header session:

```
model:     minimax/minimax-m2.5   /model to change
directory: ~/your-project
```

<figure><img src="../../../.gitbook/assets/Agentbase-image/use-codex-with-minimax.png" alt=""><figcaption><p>Codex chạy với model minimax/minimax-m2.5 qua GreenNode MaaS</p></figcaption></figure>

***

## Troubleshooting

| Triệu chứng | Nguyên nhân | Cách xử lý |
|---|---|---|
| `401 Unauthorized` | Key sai, thiếu, hoặc chưa ACTIVE | Re-export `MAAS_API_KEY`; kiểm tra status key |
| `401` dù key còn hiệu lực | **Key và `base_url` lệch loại dịch vụ** | Đối chiếu bảng đầu trang: key ở trang **API Keys** → host `maas-llm-…`; key ở tab **Subscription keys** → host `tokenplan…` |
| `403 Forbidden` (Token Plan) | Model không nằm trong gói | Đặt `model` là Model code có trong tab **Models** của gói |
| `402 Payment Required` (Token Plan) | Gói hết hạn hoặc bị xoá | Mua lại gói hoặc bật **Auto-renew** |
| `404` khi gửi request | `base_url` sai hoặc thiếu `/v1` | Codex là chuẩn OpenAI — `base_url` phải kết thúc bằng `/v1` (không có `/responses`) |
| Context bị cắt sai | Model metadata không được khai báo | Kiểm tra `model_context_window` và `model_max_output_tokens` trong config |
| Mỗi turn mất context cũ | `disable_response_storage` chưa được set | Thêm `disable_response_storage = true` vào config |
| Connection timeout | Endpoint không truy cập được | Kiểm tra VPN / kết nối đến `*.api.vngcloud.vn` (PAYG) hoặc `tokenplan.api.greennode.ai` (Token Plan) |

***

## Kết quả

Sau khi hoàn thành, Codex CLI route toàn bộ request qua endpoint GreenNode của loại dịch vụ bạn chọn. Usage được ghi nhận trên [AI Platform Console → Usage](https://aiplatform.console.greennode.ai/).

| Tôi muốn tiếp theo... | Đi đến |
|---|---|
| Dùng bản có giao diện | [Codex Desktop](../co-giao-dien/codex-desktop.md) |
| Dùng OpenCode với MaaS | [Dùng OpenCode với GreenNode MaaS](opencode.md) |
| Kết nối Claude Code với MaaS | [Kết nối Claude Code với GreenNode MaaS](claude-code.md) |
| Tìm hiểu gói Token Plan | [Token Plan](../../token-plan/README.md) |
| Xem usage và billing | [AI Platform Console](https://aiplatform.console.greennode.ai/) |

***

## Cần hỗ trợ?

Nếu bạn làm theo mà vẫn chưa được, đừng ngại liên hệ bộ phận Hỗ trợ Khách hàng của GreenNode:

* Email: [support@greennode.ai](mailto:support@greennode.ai)
* Hotline: 19001549
* Trung tâm hỗ trợ: [helpdesk.greennode.ai](https://helpdesk.greennode.ai)

Cảm ơn bạn đã sử dụng dịch vụ của GreenNode.
