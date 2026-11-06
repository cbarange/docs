---
description: >-
  Từ 19/4/2024, GreenNode chuyển đổi nền tảng trang hướng dẫn sử dụng dịch vụ.
  Các nội dung không thay đổi và vẫn được tiếp tục cập nhật tại giao diện mới.
---

# 🇻🇳 Greennode Help Center

## Tổng quan

GreenNode là nền tảng điện toán đám mây do VNG phát triển, cung cấp cho doanh nghiệp các giải pháp và dịch vụ đa dạng trên nền tảng điện toán đám mây tiên tiến, giúp doanh nghiệp thực hiện quá trình chuyển đổi số hiệu quả.

Các dịch vụ đang được cung cấp trên GreenNode Cloud, bao gồm:

\-        "vCDN": là dịch vụ phân phối dịch vụ nội dung trên cơ sở mạng lưới gồm nhiều máy chủ lưu trữ đặt tại nhiều vị trí địa lý khác nhau trong và ngoài lãnh thổ Việt Nam, cùng làm việc chung để phân phối và truyền tải dữ liệu thông tin, hình ảnh, phim (movies, clips), truyền hình trực tiếp (real-time media streaming) và các nội dung khác một cách nhanh chóng và hiệu quả nhất đến người dùng cuối.

\-        Web Application Firewall (WAF) là một giải pháp bảo mật được thiết kế để bảo vệ ứng dụng web và API bằng cách giám sát, phân tích, lọc và chặn các lưu lượng HTTP/HTTPS độc hại trước khi chúng truy cập vào máy chủ ứng dụng (origin server).

\-        "vServer": là dịch vụ cung cấp hạ tầng máy chủ ảo theo yêu cầu và có thể mở rộng về khả năng xử lý với các cấu hình phần cứng ảo như CPU, RAM, Disk. Giúp linh hoạt trong việc lựa chọn các cấu hình máy chủ ảo khác nhau và đảm bảo sự an toàn trong quản lý dịch vụ bởi kiến trúc ảo hoá đám mây của GreenNode.

\-        "vStorage": là giải pháp lưu trữ linh hoạt, an toàn, truy cập nhanh trên điện toán đám mây.

\-        “BlockStorage” là một ổ lưu trữ dạng khối (block) linh hoạt, an toàn và có tốc độ truy cập nhanh trên nền tảng điện toán đám mây.

\-        "vDB": là dịch vụ giúp dễ dàng thiết lập, vận hành và mở rộng cơ sở dữ liệu doanh nghiệp trên nền tảng điện toán đám mây của GreenNode. Với vDB, Khách hàng có thể hoàn toàn tập trung vào việc xây dựng và chạy ứng dụng của mình.

\-        "vLB": là dịch vụ tự động phân phối lưu lượng truy cập đến ứng dụng trên nhiều vServer, đáp ứng số lượng lớn tải của ứng dụng. vLB cung cấp khả năng mở rộng, chịu lỗi và bảo mật cho ứng dụng, giúp Khách hàng giảm thiểu thời gian và tiền bạc trong việc đầu tư nghiên cứu giải pháp cân bằng tải.

\-        Global Load Balancer (GLB) là một công cụ phân phối lưu lượng mạng trên quy mô đa vùng địa lý. Khác với Load Balancer truyền thống chỉ hoạt động trong một vùng (region) hoặc một mạng cục bộ, GLB có khả năng phân phối lưu lượng đến các máy chủ nằm rải rác trên nhiều vùng địa lý khác nhau.

\-        "vMonitor": cung cấp một giải pháp toàn diện về thu thập, phân tích và cảnh báo trên các dữ liệu Metric và Log từ GreenNode, các Cloud khác hay môi trường on-premise.

\-        "vContainer (K8S)": là một nền tảng mã nguồn mở dựa trên Kubernetes, giúp tự động hóa việc quản lý, scaling và triển khai ứng dụng dưới dạng container.

\-        “VKS”: là dịch vụ được quản lý bởi GreenNode giúp bạn đơn giản hóa quá trình triển khai và quản lý các ứng dụng dựa trên container.

\-        VPN Site to Site là một mô hình kết nối VPN dùng để liên kết hai hay nhiều mạng riêng tư thông qua liên kết mã bảo mật và an toàn.

\-        Public NAT instance trên GreenNode là một dịch vụ mạng cho phép các instance trong private subnet giao tiếp với các dịch vụ ngoài internet và chặn các truy cập từ internet vào những instance này.

\-        Private Endpoint là điểm kết nối giữa VPC với các dịch vụ của GreenNode gồm như: vStorage, vMonitor, vServer, vCR, IAM,…

\-        vDCI (máy chủ vật lý/bare metal servers) là một dạng dịch vụ đám mây, trong đó người dùng thuê một máy chủ vật lý từ nhà cung cấp và máy chủ này không được chia sẻ với bất kỳ khách hàng (tenant) nào khác.

\-        BackupCenter: dịch vụ được thiết kế để bảo vệ dữ liệu trên toàn bộ các tài nguyên của GreenNode.

\-        vMarketPlace: là một trung tâm cung cấp các giải pháp đám mây của Greennode nhằm tối ưu hóa quy trình truy cập của người dùng đến nhiều ứng dụng và dịch vụ từ bên thứ ba.

\-        "GreenNode AI Platform": là nền tảng điện toán đám mây hỗ trợ phát triển, huấn luyện và triển khai mô hình AI trên hạ tầng GPU của GreenNode. AI Platform cung cấp Notebook để lập trình và thử nghiệm, Network Volume để lưu trữ và chia sẻ dữ liệu giữa các phiên làm việc, Model Catalog và Model Registry để quản lý phiên bản mô hình, cùng Inference để triển khai mô hình thành API phục vụ ứng dụng thực tế. Tích hợp Guardrail giúp kiểm soát nội dung, đảm bảo AI an toàn khi vận hành.

\-        "GreenNode AgentBase": là nền tảng hạ tầng chuyên biệt dành cho AI Agent, cung cấp đầy đủ lớp vận hành, bảo mật và kiểm soát cần thiết để đưa agent từ code lên production. AgentBase bao gồm Agent Runtime (deploy, scaling, rollback), Marketplace (triển khai agent dựng sẵn như OpenClaw chỉ với một cú click), Access Control (quản lý Agent Identity và credential), MCP Governance (kiểm soát MCP tool calls qua MCP Gateway và Policy Group), Protect & Govern (rate limiting), Memory (ghi nhớ xuyên session), Container Registry, Team & Permissions và Usage & Budget (theo dõi chi phí, đặt budget limit và cảnh báo tự động).

<figure><img src=".gitbook/assets/greennode cover.png" alt=""><figcaption></figcaption></figure>

{% tabs %}
{% tab title="Infrastructure as a Service " %}
[**vServer - Hệ thống máy chủ ảo thông minh**](vserver/)

* [vServer HCM03-1A](vserver/compute-hcm03-1a/vserver-la-gi.md)

[**vStorage - Bộ giải pháp lưu trữ toàn diện trên đám mây**](vstorage/)

* [vStorage (Giao diện mới)](vstorage/)
{% endtab %}

{% tab title="Platform as a Service" %}
[**vMonitor Platform - Theo dõi toàn diện hệ thống và các tài nguyên trên Cloud**](vmonitor/)

[**vCDN - Giải pháp tăng tốc độ phân phối nội dung cho người dùng**](vcdn/)

[**vDB - Giải pháp quản lý cơ sở dữ liệu chuyên nghiệp và toàn diện cho doanh nghiệp**](vdb/)

[**VKS - Quản lý và điều phối Container với Kubernetes**](vks/)
{% endtab %}

{% tab title="Software as a Service" %}
[Veka.ai - Giải pháp Camera trực tuyến](https://help.vcloudcam.vn/#/support-center)
{% endtab %}
{% endtabs %}
