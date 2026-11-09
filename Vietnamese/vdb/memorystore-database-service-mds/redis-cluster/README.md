# Redis Cluster

**Redis Cluster** trên vDB là kiểu triển khai Redis với kiến trúc **Non-sharding** gồm 1 Master node và từ 2 đến 9 Replica nodes (tổng 3–10 nodes), hỗ trợ tự động failover và tích hợp vBackup để bảo vệ dữ liệu toàn diện.

***

## Kiến trúc

Redis Cluster trên vDB hoạt động theo mô hình **1 Master – N Replicas**:

* **Master node:** Xử lý toàn bộ read và write operations.
* **Replica nodes (2–9):** Đồng bộ dữ liệu bất đồng bộ từ Master, phục vụ read operations và sẵn sàng failover.
* **Automatic Failover:** Khi Master gặp sự cố, một Replica được tự động promote thành Master mới. Cluster tiếp tục hoạt động sau khi failover hoàn tất.

```
┌─────────────┐
│   Master    │  ← Read / Write
└──────┬──────┘
       │ Async Replication
  ┌────┴────┐
  ▼         ▼
Replica 1  Replica 2 ...  ← Read / Failover
```

{% hint style="info" %}
**Phạm vi:** Redis Cluster trên vDB hiện hỗ trợ chế độ **Non-sharding** (1 shard duy nhất). Tính năng Sharding trên nhiều shard sẽ được phát triển trong giai đoạn tiếp theo.
{% endhint %}

***

## So sánh Single-node và Cluster

| Tính năng          | Single-node                | Cluster (Non-sharding)                |
| ------------------ | -------------------------- | ------------------------------------- |
| Số Master          | 1                          | 1                                     |
| Số Replica tối đa  | 5                          | 9                                     |
| Automatic Failover | Không                      | Có                                    |
| Read Scaling       | Có (qua replica)           | Có (qua replica)                      |
| Write Scaling      | Không (single writer)      | Không (single writer)                 |
| Backup tích hợp    | Không                      | Có (vBackup — Policy + Location)      |
| Phù hợp với        | Dev, staging, workload nhỏ | Production, yêu cầu High Availability |

***

## Scaling Replicas

Bạn có thể thay đổi số lượng Replica của Cluster sau khi tạo mà **không gây downtime**. Hệ thống thực hiện scale theo từng bước:

* **Scale up:** Thêm từng replica một, mỗi replica hoàn tất sync trước khi thêm node tiếp theo.
* **Scale down:** Xóa replica theo thứ tự index giảm dần, mỗi bước chờ hoàn tất trước khi tiếp tục.

{% hint style="warning" %}
Mỗi lần thay đổi chỉ được tăng hoặc giảm **1 node** (current−1 hoặc current+1), và cluster phải giữ từ **3 đến 10 nodes**. Không thể nhảy nhiều bước trong một lần. Đây là cơ chế bảo vệ để đảm bảo sync hoàn tất trước mỗi bước tiếp theo, tránh lỗi replication khi scale đột ngột.
{% endhint %}

***

## Tích hợp Backup (vBackup)

Redis Cluster tích hợp sẵn với **vBackup** để bảo vệ dữ liệu:

* **Auto Backup:** Chạy theo lịch của Backup Policy đã chọn khi tạo cluster.
* **Manual Backup:** Tạo Full Snapshot thủ công bất kỳ lúc nào từ trang chi tiết cluster.
* **Restore:** Tạo cluster mới từ một restore point có sẵn trong Backup Center.

| Thông tin          | Chi tiết                                     |
| ------------------ | -------------------------------------------- |
| Loại backup        | Full Snapshot (không hỗ trợ Incremental)     |
| Storage            | vStorage (S3-compatible) với Object Lock     |
| Giới hạn đồng thời | 1 backup job tại 1 thời điểm                 |
| Khi hết quota      | Trả lỗi `QuotaExceeded`, không tự động retry |

***

## Tìm hiểu thêm

| Tôi muốn...                           | Đi đến...                                                                                                                                                 |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tạo Redis Cluster mới                 | [Khởi tạo Redis Cluster](khoi-tao-redis-cluster.md)                                                                                                       |
| Quản lý topology, backup, xóa cluster | [Quản lý Redis Cluster](quan-ly-redis-cluster.md)                                                                                                         |
| Xem giới hạn và hạn chế               | [Giới hạn và hạn chế](https://github.com/vngcloud/docs/blob/main/Vietnamese/vdb/memorystore-database-service-mds/redis-cluster/gioi-han-redis-cluster.md) |
