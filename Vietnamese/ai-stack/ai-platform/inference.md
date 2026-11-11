# Inference

Tính năng **Inference** giúp bạn triển khai mô hình AI thành một dịch vụ API sẵn sàng sử dụng, phục vụ trực tiếp cho các ứng dụng backend, website, hoặc pipeline phân tích.

**Tổng quan về Inference**

* Deploy mô hình từ **Model Registry** dưới dạng một **REST API endpoint**.
* Hỗ trợ chọn loại máy chủ tính toán (**CPU/GPU**) theo yêu cầu hiệu năng.
* Tự động scale theo lưu lượng request.
* Có thể tích hợp với **MaaS** để quản lý bảo mật, rate limit, auth token…

***

## Các bước triển khai Inference

#### **Bước 1: Truy cập giao diện Inference**

1. Đăng nhập vào [GreenNode AI Platform](https://aiplatform.console.greennode.ai/overview)..
2. Vào **Inference** tại menu bên trái.
3. Nhấn nút **“Create endpoint”**.

#### **Bước 2: Điền thông tin khởi tạo Inference**

| Field             | Mô tả                                                          |
| ----------------- | -------------------------------------------------------------- |
| **Endpoint Name** | Tên endpoint định danh (1–50 ký tự, không chứa ký tự đặc biệt) |
| **Region**        | Khu vực triển khai (hiện tại: `HCM`)                           |
| **Model**         | Chọn model đã được import sẵn trong **Model Registry**         |

#### **Bước 3: Cấu hình tài nguyên và tự động scale**

* **Resource Configuration**

<table><thead><tr><th width="209">Tham số</th><th>Mô tả</th></tr></thead><tbody><tr><td><strong>Instance Type (CPU / GPU / RAM)</strong></td><td>Chọn loại máy tính chạy model (ví dụ: <code>g1-standard-4x16-1rtx2080ti</code>). Tùy vào model, chọn cấu hình phù hợp với yêu cầu inference</td></tr></tbody></table>

* **Replica Configuration**

<table><thead><tr><th width="294">Tham số</th><th>Mô tả</th></tr></thead><tbody><tr><td><strong>Min Replica Count</strong></td><td>Số lượng instance tối thiểu luôn sẵn sàng</td></tr><tr><td><strong>Max Replica Count</strong></td><td>Số lượng instance tối đa khi autoscaling</td></tr><tr><td><strong>Auto-scaling Settings (Advanced configuration)</strong></td><td>Hệ thống tự tăng giảm theo các thông số threshold CPU, RAM, GPU Utilization, Response latency.</td></tr></tbody></table>

#### **Bước 4: Chọn Security**

* Nếu chọn Private Access, Endpoint URL sẽ xác thực bằng API Key (Cần tạo trước 1 API Key mới có thể tạo Inference)
* Không chọn thì Endpoint URL co thể truy cập public (không cần API Key)\
  ![](<../../.gitbook/assets/image (453).png>)

#### **Bước 5: Tạo và khởi chạy Inference**

* Nhấn **"Create"** để bắt đầu triển khai.
* Quá trình deploy mất vài phút.
* Sau khi hoàn tất, bạn sẽ nhận được thông tin **Endpoint URL để serving.**

***

## Hướng dẫn serving endpoint

### [**Bước 1: Lấy API Key**](bat-dau-voi-ai-platform.md#lay-api-key) **(Nếu là Private Endpoint)**

### **Bước 2: Lấy Endpoint URL**

**Có thể lấy Endpoint URL theo 2 cách:**

1. Nhấn nút **URL** tại danh sách các Inference.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. Sao chép tại sang chi tiết của một Inference cụ thể.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

### Bước 3: Gọi Inference

Sau khi Inference Active và có **Endpoint URL. B**ạn có thể sử dụng Inference.

* Private Inference
  * ```
    curl --location 'https://inference-aiplatform-hcm.api.vngcloud.vn/v1/<uid-inference>' ^
    --header 'Authorization: Bearer <api-key>'
    ```
* Public Inference
  * ```
    curl --location 'https://inference-aiplatform-hcm.api.vngcloud.vn/v1/<uid-inference>'
    ```
