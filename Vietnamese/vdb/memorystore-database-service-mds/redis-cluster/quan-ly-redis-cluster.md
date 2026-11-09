# Quản lý Redis Cluster

Sau khi tạo Redis Cluster, bạn có thể quản lý topology, thay đổi số replica, thay đổi instance type, thực hiện backup thủ công, theo dõi metric và xóa cluster trực tiếp từ trang chi tiết.

---

## Điều kiện tiên quyết

* Đã có ít nhất 1 Redis Cluster ở trạng thái Available.
* Quyền quản lý database trên tài khoản vDB.

---

## Lọc danh sách Database

Trang danh sách Database hỗ trợ lọc nhanh theo loại deployment bằng các tab ở đầu trang:

| Tab           | Hiển thị                                  |
| ------------- | ------------------------------------------- |
| All Databases | Tất cả database (Single-node và Cluster) |
| Single-node   | Chỉ Single-node database                   |
| Cluster       | Chỉ Cluster database                       |

Số lượng database hiển thị dạng badge bên cạnh mỗi tên tab. Quick filter kết hợp với tính năng tìm kiếm: khi đang ở tab Cluster, kết quả tìm kiếm chỉ trả về Cluster databases.

---

## Xem Topology

Tab Topology trong trang chi tiết cluster hiển thị toàn bộ cấu trúc master–replica của cluster.

![](../../../.gitbook/assets/Redis-cluster/redis-cluster-detail.png)

Để xem Topology:

1. Truy cập trang chi tiết Redis Cluster.
2. Chọn tab Topology.

Tab này hiển thị:

* Master node: Icon database màu xanh lá với nhãn "Master".
* Replica nodes: Các icon bên dưới, hiển thị tối đa 2, phần còn lại được gộp thành "+N".
* Summary: Số lượng Master, Replica và tổng số nodes.

---

## Thay đổi số Replica (Edit Number of Nodes)

Bạn có thể scale up hoặc scale down số Replica của cluster sau khi tạo mà không gây downtime.

![](../../../.gitbook/assets/Redis-cluster/redis-edit-node.png)

Để thay đổi số Replica:

1. Tại trang chi tiết cluster, chọn Edit Number of Nodes. Nút này có ở thanh thao tác phía trên trang và ở tab Topology; cả hai đều mở cùng một dialog.
2. Trong dialog Edit Number of Nodes, nhập Number of nodes mới. Trường chỉ nhận current−1 hoặc current+1, trong khoảng 3 đến 10 nodes.
3. Kiểm tra phần bên phải để xem chi phí thay đổi:
   * CURRENT NUMBER OF NODES: Số node và giá hiện tại.
   * NEW NUMBER OF NODES: Số node và giá mới.
   * Total: Chênh lệch chi phí (xanh = tăng, đỏ = giảm).
4. Chọn SAVE để áp dụng.

{% hint style="info" %} Mỗi lần edit chỉ được thay đổi ±1 node, và cluster phải giữ tối thiểu 3 nodes (1 Master + 2 Replicas). Để đạt số node mong muốn, lưu và lặp lại thao tác. Cơ chế này đảm bảo sync hoàn tất trước mỗi bước, tránh lỗi replication. {% endhint %}

---

## Thay đổi Instance Type (Resize Instance Type)

Bạn có thể tăng hoặc giảm instance type (CPU/RAM) của cluster ngay trên portal mà không phải tạo cluster mới, không phải chuyển dữ liệu và không phải đổi endpoint trong ứng dụng.

{% hint style="info" %} Resize Instance Type cho Redis Cluster hiện chỉ khả dụng tại region **HAN**. {% endhint %}

* Resize áp cho **cả cluster**: mọi node cùng đổi sang một instance type, không resize riêng từng node.
* Áp cho cả hai chiều: tăng lên package lớn hơn hoặc giảm xuống package nhỏ hơn.
* Chỉ resize được khi cluster đang ở trạng thái **ACTIVE**.

Để thay đổi Instance Type:

1. Mở dialog **Resize instance type** theo một trong hai cách:
   * Tại trang danh sách Database, chọn biểu tượng Action (⋮) của cluster, rồi chọn **Resize Instance Type**.

     ![Menu Action có mục Resize Instance Type](../../../.gitbook/assets/vdb-redis-cluster-resize-action-menu.png)
   * Tại trang chi tiết cluster, chọn nút **Resize Instance Type** trên header.

     ![Nút Resize Instance Type trên header trang chi tiết](../../../.gitbook/assets/vdb-redis-cluster-resize-header-button.png)
2. Trong dialog, chọn **Instance family** và **CPU Platform**, rồi chọn package mới trong bảng. Cột **Description** cho biết dung lượng bộ nhớ dùng được cho dữ liệu của từng package.
3. Kiểm tra chi phí ở bên phải dialog:
   * **CURRENT INSTANCE TYPE**: package và giá của instance type đang dùng.
   * **NEW INSTANCE TYPE**: package và giá của package vừa chọn.
   * **Total**: bằng giá của package mới.

   Giá hiển thị là giá tháng của một node nhân với số node của cluster.
4. Chọn **RESIZE** để áp dụng, hoặc **CANCEL** để đóng dialog mà không thay đổi gì.

![Dialog Resize instance type](../../../.gitbook/assets/vdb-redis-cluster-resize-dialog.png)

Một số package trong bảng bị làm mờ và không chọn được:

* Package đang dùng.
* Package nhỏ hơn mà không chứa đủ lượng dữ liệu cluster đang có.

Khi tăng instance type, hệ thống kiểm tra quota cho phần CPU/RAM tăng thêm nhân với số node. Nếu không đủ quota, yêu cầu bị từ chối kèm thông báo và cluster giữ nguyên cấu hình cũ. Khi giảm instance type, hệ thống không kiểm tra quota.

### Trong và sau khi resize

* Cluster chuyển sang trạng thái **BUILDING**. Trong thời gian này, mọi action ở menu Action và trên header (kể cả Resize Instance Type và Delete) đều bị làm mờ và không thao tác được.
* Trong lúc các node khởi động lại lần lượt, thao tác ghi vào Redis có thể lỗi trong một khoảng ngắn mỗi khi chuyển master. Endpoint kết nối giữ nguyên.
* Khi hoàn tất, cluster trở về **ACTIVE** và mục Instance type hiển thị package mới. Toàn bộ dữ liệu được giữ nguyên.
* Giá của package mới bắt đầu tính từ lúc resize thành công. Dung lượng Free Backup cũng thay đổi theo package mới.
* Tab **History** ghi lại thao tác với Action **Resize**, Description "Change package from" kèm tên package cũ và package mới. Status chuyển từ **IN-PROGRESS** sang **FINISHED**.

![Dòng Resize trong tab History](../../../.gitbook/assets/vdb-redis-cluster-resize-history.png)

Nếu resize gặp lỗi, hệ thống tự đưa cluster về instance type cũ và trạng thái **ACTIVE**, dữ liệu không bị ảnh hưởng và giá không đổi. Dòng Resize trong tab History chuyển sang **FAILED**, nội dung lỗi hiển thị ở cột **Message**.

---

## Backup thủ công (Back up now)

Nút Back up now nằm trực tiếp trên header trang chi tiết cluster, cho phép tạo Full Snapshot ngay lập tức mà không cần vào submenu.

Để tạo Manual Backup:

1. Tại header trang chi tiết cluster, chọn Back up now.
2. Xác nhận trong dialog
3. Chọn Back up now.

Hệ thống sẽ hiển thị thông báo và backup mới xuất hiện trong tab Backup .

![](../../../.gitbook/assets/Redis-cluster/redis-backup-now.png)

{% hint style="warning" %}

* Nút Back up now bị vô hiệu hóa khi đang có backup job đang chạy (Auto hoặc Manual). Tooltip sẽ hiển thị: "A backup job is currently in progress".
* Khi tài khoản hết quota, hệ thống trả lỗi `<span attribution="{"id":"huyenttn3@vng.com.vn","name":"Huyền. Trần Thị Ngọc (3)","timestamp":1778229000000,"email":"huyenttn3@vng.com.vn","oid":"15e1faed-f48a-4412-b344-1ef30f492985","dataSource":0}">QuotaExceeded</span>` và không tự động retry.
* Chỉ hỗ trợ Full Snapshot — không hỗ trợ Incremental backup thủ công. {% endhint %}

---

## Xem thông tin Backup

Tab Backup trong trang chi tiết cluster cho phép bạn theo dõi trạng thái backup và lịch sử backup.

Để xem thông tin Backup:

1. Tại trang chi tiết cluster, chọn tab Backup.

![](../../../.gitbook/assets/Redis-cluster/redis-detail-tab-backup.png)

Tab này hiển thị:

* Backup Information: Description, Created Date, Latest Record, Backup Size, Backup Policy, Backup Location.
* Backup List: Danh sách các bản backup với các cột: Backup DB Point ID, Backup Size (GB), Backup Type, Schedule Type, Backup Location, Backup Point, Status, Action.

Chọn View full backup details → để mở trang chi tiết backup của cluster này trong Backup Center (mở tab mới).

---

## Quản lý Backup tại Backup Center

Trang Backup của vDB (menu: Memory Store > Backup) hiển thị tổng quan danh sách backup. Để thực hiện các thao tác đầy đủ (Restore, Delete, quản lý Policy), bạn cần vào Backup Center.

| Trạng thái      | Hành động                                                      |
| ----------------- | ----------------------------------------------------------------- |
| Chưa có backup  | ChọnCreate a Backupđể chuyển đến Backup Center tạo mới.   |
| Đã có backup   | ChọnGo to vBackupđể mở Backup Center (filter theo Vault vDB). |
| Click tên backup | Mở trang chi tiết backup trong Backup Center (tab mới).        |

---

## Cấu hình tham số (Configuration Group)

Redis Cluster áp dụng cấu hình tham số qua **DB Configuration Group**: bạn tạo group, gắn vào cluster và chỉnh sửa tham số ngay trên portal thay vì sửa file cấu hình thủ công. Cách tạo, chỉnh sửa và gắn group cho MDS Instance được mô tả chi tiết ở trang [Quản lý cấu hình MDS Instance (Configuration Group)](../quan-ly-cau-hinh-mds-instance.md).

### Xem và tạo Configuration Group

1. Mở trang **Configuration group** trên vDB portal.
2. Tìm group theo tên hoặc ID, hoặc tạo group mới bằng **Create a Configuration group**.

![Danh sách Configuration group](../../../.gitbook/assets/vdb-redis-cluster-config-group-list.png)

Khi tạo group cho Redis Cluster: đặt **Configuration name** và mô tả, chọn **Engine** là Redis, **Deployment type** là Cluster, rồi chọn **Engine version** phù hợp với cluster.

<figure><img src="../../../.gitbook/assets/vdb-redis-cluster-config-group-create.png" alt="Dialog Create Configuration group"><figcaption><p>Dialog Create Configuration group — chọn Deployment type là Cluster khi tạo group cho Redis Cluster</p></figcaption></figure>

### Gắn group vào Redis Cluster

1. Mở trang chi tiết Redis Cluster và chọn **Edit Configuration Group** trên header. Hành động này cũng có trong Action Menu (⋮) của cluster ở danh sách Database.
2. Trong dialog, mở dropdown **DB configuration group** và chọn group muốn gắn. Danh sách chỉ liệt kê các group cùng engine với cluster; nhấn biểu tượng làm mới nếu group mới tạo chưa xuất hiện.
3. Chọn **Save** để cập nhật group cho cluster.

![Dialog Edit Configuration Group](../../../.gitbook/assets/vdb-redis-cluster-config-group-edit-configuration-group.png)

### Sửa tham số

Mở Configuration Group đang gắn với cluster — trang chi tiết hiển thị bảng tham số với **Name**, **Values**, **Allowed values** và **Data type**.

![Trang chi tiết Configuration group với bảng tham số](../../../.gitbook/assets/vdb-redis-cluster-config-group-detail.png)

Chọn **Edit parameters** để chuyển sang chế độ chỉnh sửa, thay đổi các tham số trong khoảng **Allowed values** cho phép, rồi chọn **Preview and Save**.

![Chế độ Edit parameters](../../../.gitbook/assets/vdb-redis-cluster-config-group-edit-parameters.png)

Trong dialog **Preview changes and apply**: đối chiếu thay đổi ở bảng **Current value** / **New value**, tick checkbox xác nhận việc sửa tham số sẽ ảnh hưởng tới các database đang gắn group, rồi chọn **Apply**.

![Dialog Preview changes and apply](../../../.gitbook/assets/vdb-redis-cluster-config-group-edit-parameters-preview.png)

{% hint style="info" %}
**Hành vi khi bỏ một parameter khỏi Configuration Group**: Với Redis Cluster, khi bạn bỏ một parameter khỏi Configuration Group đang gắn với cluster, parameter đó **giữ nguyên giá trị đang có** và **không** trở về giá trị mặc định của Database Engine. Điều này khác với Redis Single node, nơi parameter bị bỏ sẽ trở về giá trị mặc định của Database Engine.

Mỗi lần chỉnh sửa, bạn có thể thay đổi một hoặc nhiều parameter. Các parameter không được thay đổi trên portal sẽ giữ nguyên giá trị đang có.
{% endhint %}

---

## Giám sát cluster (Monitoring)

Khi bạn tạo Redis Cluster, hệ thống tự động thu thập metric của cluster qua vMonitor Platform và hiển thị ở tab **Monitor** trong trang chi tiết. Một số metric được bật sẵn, bạn không cần cấu hình thêm.

Để xem metric:

1. Truy cập trang chi tiết Redis Cluster.
2. Chọn tab **Monitor**.
3. Chọn **Cluster metric** để xem metric của cả cluster, hoặc **Node metric** để xem metric của từng node.
4. Chọn khoảng thời gian cần xem ở góc phải, từ 15 phút đến 1 ngày.

Các biểu đồ chính có sẵn ở tab Monitor:

| Chế độ xem | Nhóm | Biểu đồ |
| --- | --- | --- |
| Cluster metric | Kết nối và lệnh | Connection Usage, Executed Commands, Top Commands, Slow Commands |
| Cluster metric | CPU và bộ nhớ | Redis CPU Saturation (Main Thread), Redis Max Memory, Redis Used Memory, Redis Memory Fragmentation Ratio |
| Cluster metric | Replication và cache | Master/Replica Offset, Hit Ratio |
| Node metric | Trạng thái node | Online Nodes, Master Node, Replica Nodes |
| Node metric | Tài nguyên | Instance CPU Usage, Instance Memory Usage |

Để xem thêm metric ngoài tập bật sẵn, tự vẽ dashboard hoặc tạo cảnh báo (alarm), chọn **Monitored by vMonitor Platform** để chuyển sang vMonitor Platform và bật Detailed Monitoring. Tham khảo:

* [Giám sát hoạt động vDB bằng vMonitor Platform](../../relational-database-service-rds/working-with-rds/giam-sat-hoat-dong-vdb-bang-vmonitor-platform.md)
* [Danh sách metrics của vDB](../../../vmonitor-platform/cach-tinh-nang-cua-vmonitor-platform/metrics/danh-sach-metrics-ho-tro/danh-sach-metrics-cua-vdb.md)

---

## Xóa Redis Cluster

{% hint style="warning" %} Thao tác xóa cluster không thể hoàn tác. Toàn bộ dữ liệu sẽ bị xóa vĩnh viễn. {% endhint %}

Để xóa cluster:

1. Tại trang chi tiết cluster, chọn biểu tượng More Actions (⋮).
2. Chọn Delete.
3. Trong dialog xác nhận:
   * (Tùy chọn) Chọn Create final backup? để tạo snapshot cuối trước khi xóa (chỉ khả dụng nếu cluster còn Backup Database trong vBackup).
   * Chọn I acknowledge... để xác nhận mất quyền truy cập vào các bản backup.
   * Nhập chữ delete vào ô xác nhận.
4. Chọn Delete (màu đỏ) để hoàn tất.

{% hint style="info" %} Nếu Backup Database của cluster đã bị xóa khỏi vBackup, checkbox Create final backup? sẽ bị vô hiệu hóa và hệ thống hiển thị ghi chú: "No backup database found. Final backup is unavailable." {% endhint %}

---

## Action Menu

Từ trang danh sách Database, chọn biểu tượng Action (⋮) bên cạnh một Redis Cluster để thực hiện nhanh:

| Action                   | Mô tả                                                     |
| ------------------------ | ----------------------------------------------------------- |
| Resize Instance Type     | Tăng hoặc giảm instance type (CPU/RAM) của cluster. Hiện chỉ khả dụng tại region HAN. |
| Edit configuration group | Chỉnh sửa Redis configuration parameters.                 |
| Edit DB settings         | Chỉnh sửa tên, backup policy và các database settings. |
| Delete                   | Xóa cluster (có dialog xác nhận).                       |

---

## Tiếp theo

* [Xem giới hạn và hạn chế khi dùng Redis Cluster](https://loop.cloud.microsoft/p/gioi-han-redis-cluster.md)
* [Tìm hiểu kiến trúc và so sánh với Single-node](https://loop.cloud.microsoft/p/redis-cluster.md)
