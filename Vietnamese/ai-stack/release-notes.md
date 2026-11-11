# Lịch sử cập nhật — GreenNode AI Stack

Tổng hợp các bản cập nhật của toàn bộ sản phẩm trong GreenNode AI Stack — GreenNode AgentBase, Model as a Service, AI Platform, AI Coding và GenAI Studio.

***

## Tháng 10, 2026

**GreenNode AI Platform — Guardrail**

AI Platform ra mắt **Guardrail** — tính năng bảo vệ dữ liệu và kiểm soát nội dung trước khi request tới model trên MaaS, không cần sửa code client:

* **Word filters:** chặn/che theo từ khoá hoặc cụm từ bạn khai báo (hành động Mask / Detect / Block).
* **Prompt-based filter:** model "giám sát" đọc và đánh giá nội dung theo mô tả của bạn — bắt được cả prompt vượt rào (jailbreak) được diễn đạt lại.
* **Sensitive information:** che/thay/xoá tự động dữ liệu PII (email, số thẻ tín dụng, số điện thoại…) và regex tuỳ chỉnh trước khi dữ liệu tới model.
* Gắn Guardrail vào model trên MaaS là mọi request tự động đi qua — endpoint và API key giữ nguyên.
* Tìm hiểu thêm tại [Guardrail](ai-platform/guardrail/README.md).

**GreenNode AgentBase — Built-in Tools (Code Interpreter & Browser)**

AgentBase ra mắt **Built-in Tools** — bộ tool dựng sẵn cho AI Agent chạy code và điều khiển trình duyệt trong sandbox được cô lập theo từng session:

* **Code Interpreter:** chạy Python và lệnh shell, đọc/ghi file, chạy tiến trình nền — mỗi session trong một sandbox riêng, cô lập hoàn toàn.
  * Tìm hiểu thêm tại [Sử dụng Code Interpreter](agent-base/built-in-tools/su-dung-code-interpreter.md).

* **Browser:** điều khiển Chrome thật qua CDP (Playwright/Puppeteer), xem trực tiếp màn hình qua live view và tải lại bản ghi phiên (recording).
  * Tìm hiểu thêm tại [Sử dụng Browser](agent-base/built-in-tools/su-dung-browser.md).

* Tích hợp qua **MCP**: kết nối agent bằng endpoint MCP, xác thực qua flow đăng nhập GreenNode Portal — token từ Root user, IAM user hoặc Service Account đều dùng được.

* Mỗi session chạy trong sandbox riêng, được cô lập hoàn toàn — trạng thái của session này không lệch sang session khác.
  * Tìm hiểu thêm tại [Built-in Tools](agent-base/built-in-tools/README.md).

* Tính năng đang mở theo **whitelist** account — liên hệ [support@greennode.ai](mailto:support@greennode.ai) để được bật.

***

## Tháng 8, 2026

**GreenNode AgentBase — CLI & MCP**

AgentBase bổ sung 2 cách làm việc bên cạnh Portal — dòng lệnh và AI assistant:

* **GreenNode CLI:** nhóm lệnh `grn agentbase` quản lý trọn vòng đời agent — Identity, Runtime, Memory, MCP Gateway, Policy và Container Registry — và deploy cả agent bằng một manifest duy nhất qua `grn agentbase deploy up`. Phù hợp để đưa việc deploy agent vào script tự động hoá và CI/CD.
  * Tìm hiểu thêm tại [Sử dụng GreenNode CLI để quản lý AgentBase](agent-base/su-dung-greennode-cli-de-quan-ly-agentbase.md).

* **AgentBase MCP:** máy chủ MCP cho phép AI assistant (Claude Desktop, Claude Code, Cursor, Windsurf...) thao tác với AgentBase bằng ngôn ngữ tự nhiên qua 3 meta-tool `list_servers` / `search_tools` / `execute`. Hỗ trợ cả kết nối local (stdio) và remote (HTTP qua MCP Gateway).
  * Tìm hiểu thêm tại [Sử dụng GreenNode MCP để quản lý AgentBase](agent-base/su-dung-greennode-mcp-de-quan-ly-agentbase.md).

**GreenNode MaaS — Cập nhật danh mục model & bảng giá**

GreenNode cập nhật danh mục model trên MaaS thành hai nhóm — **model do GreenNode self-host** và **model third-party** đã ký hợp đồng chính thức — kèm bảng giá tốt hơn cho nhiều model. Portal bổ sung **label loại model (Self-host / Partner)** ngay trên danh sách, giúp bạn dễ dàng phân biệt và chọn model phù hợp.

* Model hiện tại ngừng cung cấp & danh mục mới lên portal từ **03/08/2026**, gia hạn tự động đến **02/09/2026** trước khi request đến model cũ bắt đầu trả lỗi.
* Đối chiếu model đang dùng với [danh mục mới](model-as-a-service/cac-model-duoc-cung-cap.md); chỉ cần đổi model nếu model hiện tại không còn trong danh mục.
* Xem chi tiết cách tính phí tại [Cách tính phí](model-as-a-service/cach-tinh-phi.md); đơn giá của từng model được hiển thị trực tiếp trên [Portal AI Platform](https://aiplatform.console.greennode.ai/models).

{% hint style="info" %}
Cần hỗ trợ trong quá trình chuyển đổi? Liên hệ [support@greennode.ai](mailto:support@greennode.ai), hotline **19001549**, hoặc [Trung tâm hỗ trợ](https://helpdesk.greennode.ai/portal/vi/home).
{% endhint %}

**GreenNode AI Gateway — Nâng cấp kiến trúc, tối ưu hiệu năng**

GreenNode nâng cấp kiến trúc AI Gateway để đáp ứng tốt hơn lưu lượng ngày càng lớn từ MaaS và Token Plan:

* **Low Latency:** Giảm độ trễ authentication API key và rate limiter.
* **Scalability:** Không còn giới hạn bởi memory — phục vụ nhiều model, nhiều user hơn.
* **High Performance:** Đáp ứng tốt traffic từ Token Plan.

**GreenNode AI Stack — Token Plan**

Token Plan giúp khoá trước ngân sách AI hàng tháng bằng gói **prepaid 30 ngày** với hạn mức token/request cố định theo từng model — thay vì trả theo token thực dùng như PAYG.

* Truy cập qua **subscription-key** riêng biệt hoàn toàn với API Key PAYG.
* Browse & mua gói ngay trong nhóm **Token Plan** trên Portal.
* Phù hợp với usage ổn định hàng ngày (ví dụ: coding assistant).
* Tìm hiểu thêm tại [Token Plan](token-plan/README.md).

***

## Tháng 7, 2026

**GreenNode AgentBase — Security Runtime & MCP Connectors**

GreenNode AgentBase bổ sung các tính năng bảo mật và tích hợp mới cho AI Agent:

**Tính năng mới:**

* **Security Runtime:** Kiểm soát client nào được phép truy cập Runtime và cách xác thực request đến — cấu hình **IP Access Control** (dải IP CIDR nguồn được phép) và **Inbound Identity** (IAM Permissions, JWT, hoặc No authorization) ngay khi tạo Runtime.
  * Tìm hiểu thêm tại [Khởi tạo Runtime](agent-base/agent-runtime/khoi-tao-runtime.md).

* **MCP Connectors:** Kết nối agent với external service — GitHub, Slack, Microsoft 365 (bundle M365 VNG)... — chỉ trong vài phút thông qua catalog dựng sẵn, không cần build MCP server hay tự code OAuth.
  * Tìm hiểu thêm tại [MCP Connectors](agent-base/mcp-connectors/README.md).

***

## Tháng 5, 2026

**GreenNode AgentBase — Phase 2**

GreenNode AgentBase ra mắt **Phase 2** với các tính năng mới mở rộng khả năng quản trị, bảo mật và tích hợp cho AI Agent:

**Tính năng mới:**

* **Marketplace:** Browse và deploy AI agent từ thư viện template theo danh mục — AI Chat, Coding, Automation. Hỗ trợ filter, xem chi tiết và deploy 1 click.
  * Tìm hiểu thêm tại [Marketplace](agent-base/marketplace/README.md).

* **Container Registry:** Kho lưu trữ container image riêng, được cấp phát tự động cho tổ chức. Hỗ trợ push image qua AgentBase Skills (khuyến nghị) hoặc Docker CLI thủ công; image dùng trực tiếp khi tạo Runtime.
  * Tìm hiểu thêm tại [Container Registry](agent-base/container-registry/README.md).

* **Rate Limit:** Kiểm soát tần suất gọi API theo số request hoặc token trên từng model và API key, ngăn quá tải hệ thống và kiểm soát chi phí.
  * Tìm hiểu thêm tại [Rate Limit](agent-base/protect-govern/rate-limit.md).

* **MCP Governance:** Kiểm soát tập trung kết nối giữa AI Agent và external service:
  * **MCP Gateway** — Proxy tập trung, tự động nhận diện protocol, áp dụng Policy và hỗ trợ Private mode qua VPC Peering.
  * **Policy Groups** — Bộ quy tắc Allow/Deny theo tool name, input pattern và output pattern.
  * Tìm hiểu thêm tại [MCP Governance](agent-base/mcp-governance/README.md).

* **AI Coding:** Kết nối Claude Code CLI và các AI coding tool OpenAI-compatible (OpenAI SDK, LiteLLM, Cursor...) trực tiếp với GreenNode MaaS — thanh toán bằng credit-token nội bộ.
  * Tìm hiểu thêm tại [AI Coding](ai-coding/README.md).

* **Usage & Budget:** Dashboard theo dõi lượng tiêu thụ token và chi phí realtime theo agent, model, API key và khoảng thời gian; cài đặt ngân sách tháng với cảnh báo tự động khi đạt 80% và 100%.
  * Tìm hiểu thêm tại [Usage & Budget](usage-budget/README.md).

* **Mạng riêng tư (Private Networking):** VPC Peering cho Agent Runtime và MCP Gateway — cho phép agent kết nối đến internal service mà không cần expose ra internet.
  * Tìm hiểu thêm tại [Mạng riêng tư](agent-base/private-networking.md).

***

## Tháng 4, 2026

**GreenNode AgentBase — OpenClaw 1-Click**

GreenNode ra mắt **OpenClaw 1-Click** trên AgentBase, cho phép triển khai AI Agent cá nhân dựa trên OpenClaw ngay từ **Agent Marketplace** — không cần kiến thức kỹ thuật, không cần cài đặt thủ công, chỉ trong 40–60 giây.

**Tính năng mới:**

* **OpenClaw 1-Click:** Deploy OpenClaw instance trực tiếp từ Marketplace với cấu hình tối giản.
  * **Tự động kết nối GreenNode MaaS:** Tài khoản GreenNode được tự động cấp quyền truy cập model AI, không cần cấu hình API key thủ công. Model mặc định: **qwen3-5-27b**.
  * **BYOK — Bring Your Own Key:** Hỗ trợ mang API key từ provider bên ngoài (OpenAI, Anthropic, Gemini...).
  * **Tích hợp Channel:** Cấu hình kết nối Telegram và Zalo ngay trong bước deploy.
  * **My Agents:** Quản lý toàn bộ OpenClaw instance với filter theo trạng thái, stop, restart và xóa.
  * Tìm hiểu thêm tại [OpenClaw 1-Click](agent-base/agent-runtime/openclaw/openclaw-1-click.md).

***

## Tháng 3, 2026

**GreenNode AgentBase — Phase 1**

GreenNode AgentBase ra mắt **Phase 1** — nền tảng hạ tầng chuyên biệt dành cho AI Agent, giúp developer triển khai và vận hành AI Agent trên cloud mà không cần tự quản lý server, scaling hay credential.

**Tính năng mới:**

* **Agent Runtime:** Deploy agent dưới dạng container với autoscaling, versioning và zero-downtime deployment; hỗ trợ Custom Agent và OpenClaw 1-Click.
* **Access Control & Team Permissions:** Quản lý danh tính agent, tự động inject credentials vào container; phân quyền thành viên theo Role với Service Accounts.
* **Memory Service:** Lưu trữ lịch sử hội thoại (short-term) và trích xuất facts ngữ nghĩa (long-term).
* **GreenNode MaaS Integration:** Kết nối trực tiếp với LLM models qua API tương thích OpenAI.
* Tìm hiểu thêm tại [GreenNode AgentBase](agent-base/README.md).
