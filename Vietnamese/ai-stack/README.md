# AI Stack

## Tổng quan

GreenNode cung cấp một nền tảng AI toàn diện giúp doanh nghiệp triển khai, tích hợp và vận hành các ứng dụng Generative AI một cách dễ dàng, bảo mật và hiệu quả. AI Stack của GreenNode được thiết kế theo hướng modular, linh hoạt và tối ưu cho cả đội ngũ kỹ thuật và nhà quản lý.

<figure><img src="../.gitbook/assets/image (1086).png" alt=""><figcaption></figcaption></figure>

***

## Các thành phần chính

#### 1. Hạ tầng AI linh hoạt với VKS

* **VKS (GreenNode Kubernetes Service)** là dịch vụ Kubernetes được quản lý toàn diện.
* Tích hợp GPU, khả năng autoscaling, giám sát tài nguyên và bảo mật.
* Tối ưu cho AI workloads cần GPU, nhưng cũng phù hợp để triển khai ứng dụng web, microservices và backend quy mô lớn.
* Giúp doanh nghiệp nhanh chóng triển khai ứng dụng mà không cần tự xây dựng và duy trì hạ tầng.

#### 2. Vector Database dưới dạng dịch vụ

* Hỗ trợ PostgreSQL(**pgvector)** và **OpenSearch** làm vector database.
* Được cung cấp dưới dạng **managed service**, không cần lo hạ tầng.
* Hỗ trợ triển khai mô hình **RAG (Retrieval-Augmented Generation)** nhanh chóng.
* Giúp mô hình GenAI hiểu và khai thác dữ liệu doanh nghiệp theo ngữ cảnh.

#### 3. AI Platform toàn diện

* Cung cấp môi trường để **thử nghiệm (notebook )**, **fine-tune** và **inference** các mô hình AI ngay trên nền tảng GPU của GreenNode.
* Tích hợp với hệ thống lưu trữ như **vStorage** để lưu dữ liệu huấn luyện, model registry và đầu ra.
* Các tính năng **fine-tune** hiện đang được phát triển (coming soon).
* Hạn chế rào cản kỹ thuật, rút ngắn thời gian triển khai.

#### 4. vStorage – Lưu trữ AI hiệu quả

* **vStorage** là dịch vụ **object storage tương thích S3** được tối ưu cho AI workloads.
* Phù hợp để lưu trữ dữ liệu huấn luyện, checkpoint mô hình, model registry và output inference.
* Dễ dàng tích hợp với các thành phần như AI Platform, VKS và các công cụ huấn luyện phổ biến.

#### 5. Model-as-a-Service

* Hỗ trợ truy cập hơn đa dạng **mô hình GenAI** hàng đầu như GPT, Claude, Gemini, DeepSeek... chỉ với 1 API thống nhất.
* Bao gồm cả mô hình mã nguồn mở và các mô hình tối ưu cho tiếng Việt.
* Các chức năng của **AI Gateway** (routing thông minh, caching, rate limit, authentication token,...) hiện đã được tích hợp và áp dụng cho toàn bộ MaaS của GreenNode.
* Rút ngắn thời gian tích hợp, giúp doanh nghiệp nhanh chóng thử nghiệm và đưa AI vào thực tế.

***

#### Giá Trị Cốt Lõi Cho Doanh Nghiệp

* Tăng tốc hành trình ứng dụng GenAI mà không cần đội ngũ AI chuyên sâu.
* Loại bỏ gánh nặng hạ tầng, tối ưu chi phí vận hành AI.
* Tăng cường kiểm soát, bảo mật và tính linh hoạt trong tích hợp AI vào sản phẩm số.

AI Stack của GreenNode là nền tảng lý tưởng để doanh nghiệp phát triển và mở rộng các ứng dụng GenAI một cách an toàn, hiệu quả và bền vững.
