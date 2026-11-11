# Integrate with Network Load Balancer

Để tích hợp một Network Load Balancer với một Kubernetes cluster, bạn có thể sử dụng một Service với type là LoadBalancer. Khi bạn tạo một Service như vậy, GreenNode LoadBalancer Controller sẽ tự động một NLB để chuyển tiếp lưu lượng đến các pod trên node của bạn. Bạn cũng có thể sử dụng các annotation để tùy chỉnh các thuộc tính của Network Load Balancer, như port, protocol,...

### Chuẩn bị <a href="#integratewithnetworkloadbalancer-chuanbi" id="integratewithnetworkloadbalancer-chuanbi"></a>

* Tạo một Kubernetes cluster trên GreenNode, hoặc sử dụng một cluster đã có. Lưu ý: đảm bảo bạn đã tải xuống cluster configuration file sau khi cluster được khởi tạo thành công và truy cập vào cluster của bạn.
* Khởi tạo hoặc sử dụng một **service account** đã tạo trên IAM và gắn policy: **vLBFullAccess**, **vServerFullAccess**. Để tạo service account bạn truy cập tại [đây](https://iam.console.greennode.ai/service-accounts) và thực hiện theo các bước sau:
  * Chọn "**Create a Service Account**", điền tên cho Service Account và nhấn **Next Step** để gắn quyền cho Service Account
  * Tìm và chọn **Policy:** **vLBFullAccess và Policy:** **vServerFullAccess**, sau đó nhấn "**Create a Service Account**" để tạo Service Account, Policy: vLBFullAccess vàPolicy: vServerFullAccess do GreenNode tạo ra, bạn không thể xóa các policy này.
  * Sau khi tạo thành công bạn cần phải lưu lại **Client\_ID** và **Secret\_Key** của Service Account để thực hiện bước tiếp theo.

***

### Khởi tạo Service Account và cài đặt GreenNode LoadBalancer Controller <a href="#exposemotservicethongquavlblayer4-khoitaoserviceaccountvacaidatvngcloudcontrollermanager" id="exposemotservicethongquavlblayer4-khoitaoserviceaccountvacaidatvngcloudcontrollermanager"></a>

{% hint style="info" %}
Chú ý:

Khi bạn thực hiện khởi tạo Cluster theo hướng dẫn bên trên, nếu bạn chưa bật option **Enable vLB Native Integration Driver**, mặc định chúng tôi sẽ không cài sẵn plugin này vào Cluster của bạn. Bạn cần tự thực hiện Khởi tạo Service Account và cài đặt GreenNode LoadBalancer Controller theo hướng dẫn bên dưới. Nếu bạn đã bật option **Enable vLB Native Integration Driver**, thì chúng tôi đã cài sẵn plugin này vào Cluster của bạn, hãy bỏ qua bước Khởi tạo Service Account, cài đặt GreenNode LoadBalancer Controller và tiếp tục thực hiện theo hướng dẫn kể từ Deploy một Workload.
{% endhint %}

<details>

<summary>Hướng dẫn khởi tạo Service Account và cài đặt GreenNode LoadBalancer Controller</summary>

**Khởi tạo Service Account**

* Khởi tạo hoặc sử dụng một **service account** đã tạo trên IAM và gắn policy: **vLBFullAccess**, **vServerFullAccess**. Để tạo service account bạn truy cập tại [đây](https://iam.console.greennode.ai/service-accounts) và thực hiện theo các bước sau:
  * Chọn "**Create a Service Account**", điền tên cho Service Account và nhấn **Next Step** để gắn quyền cho Service Account
  * Tìm và chọn **Policy:** **vLBFullAccess và Policy:** **vServerFullAccess**, sau đó nhấn "**Create a Service Account**" để tạo Service Account, Policy: vLBFullAccess vàPolicy: vServerFullAccess do GreenNode tạo ra, bạn không thể xóa các policy này.
  * Sau khi tạo thành công bạn cần phải lưu lại **Client\_ID** và **Secret\_Key** của Service Account để thực hiện bước tiếp theo.

**Cài đặt GreenNode LoadBalancer Controller**

* Cài đặt Helm phiên bản từ 3.0 trở lên. Tham khảo tại [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/) để biết cách cài đặt.
* Thay thế thông tin ClientID, Client Secret và ClusterID của cụm K8S của bạn và tiếp tục chạy:

```bash
helm install vngcloud-load-balancer-controller oci://vcr.vngcloud.vn/81-vks-public/vks-helm-charts/vngcloud-load-balancer-controller \
  --namespace kube-system \
  --set mysecret.global.clientID= __________________ \
  --set mysecret.global.clientSecret= __________________
```

* Sau khi việc cài đặt hoàn tất, thực hiện kiểm tra trạng thái của pods:

```bash
kubectl -n kube-system get pod -l app.kubernetes.io/name=vngcloud-load-balancer-controller
```

Ví dụ như ảnh bên dưới là bạn đã cài đặt thành công pod:

```bash
NAME                                                              READY   STATUS    RESTARTS   AGE
vngcloud-load-balancer-controller-1736217866-manager-77599vrxpz   1/1     Running   0          4h24m
```

</details>

***

### Deploy một Workload <a href="#exposemotservicethongquavlblayer4-deploymotworkload" id="exposemotservicethongquavlblayer4-deploymotworkload"></a>

#### **1.Nếu bạn chưa có sẵn một Network Load Balancer** đã khởi tạo trước đó trên hệ thống vLB. <a href="#integratewithnetworkloadbalancer-1.neubanchuacosanmotnetworkloadbalancerdakhoitaotruocdotrenhethongv" id="integratewithnetworkloadbalancer-1.neubanchuacosanmotnetworkloadbalancerdakhoitaotruocdotrenhethongv"></a>

Lúc này, bạn cần thực hiện:

**Bước 1**: **Tạo Deployment, Service cho Nginx app.**

* Tạo file **nginx-service-lb4.yaml** với nội dung sau:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.19.1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: LoadBalancer 
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

* Hoặc sử dụng file mấu sau đây để deploy HTTP Apache Service với Internal LoadBalancer cho phép truy cập nội bộ trên cổng 8080:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: internal-http-apache2-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: apache2
  template:
    metadata:
      labels:
        app: apache2
    spec:
      containers:
        - name: apache2
          image: httpd
          ports:
            - containerPort: 80
---

apiVersion: v1
kind: Service
metadata:
  name: internal-http-apache2-service
  annotations:
    vks.vngcloud.vn/scheme: "internal"              # MUST set like this to create an internal loadbalancer
spec:
  selector:
    app: apache2
  type: LoadBalancer                                # MUST set like this to create an internal loadbalancer
  ports:
    - name: http
      protocol: TCP
      port: 8080                                    # CAN be accessed via this port with other service in the same VPC
      targetPort: 80
```

* Hoặc tập tin YAML mẫu để tạo Deployment và Service cho ứng dụng máy chủ UDP trong một cụm Kubernetes:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: udp-server-deployment
spec:
  selector:
    matchLabels:
      name: udp-server
  replicas: 5
  template:
    metadata:
      labels:
        name: udp-server
    spec:
      containers:
      - name: udp-server
        image: vcr.vngcloud.vn/udp-server
        imagePullPolicy: Always
        ports:
        - containerPort: 10001
          protocol: UDP
---

apiVersion: v1
kind: Service
metadata:
  name: udp-server-service
  annotations:
    vks.vngcloud.vn/pool-algorithm: "source-ip"
  labels:
    app: udp-server
spec:
  type: LoadBalancer
  sessionAffinity: ClientIP
  ports:
  - port: 10001
    protocol: UDP
  selector:
    name: udp-server

```

#### **2.Nếu bạn đã có sẵn một Network Load Balancer** đã khởi tạo trước đó trên hệ thống vLB và bạn muốn tái sử dụng NLB cho cluster của bạn. <a href="#integratewithnetworkloadbalancer-2.neubandacosanmotnetworkloadbalancerdakhoitaotruocdotrenhethongvlb" id="integratewithnetworkloadbalancer-2.neubandacosanmotnetworkloadbalancerdakhoitaotruocdotrenhethongvlb"></a>

Lúc này, bạn hãy nhập thông tin Load Balancer ID vào annotation <mark style="color:red;">**vks.vngcloud.vn/load-balancer-id.**</mark> Ví dụ dưới đây là tập tin YAML mẫu để triển khai Nginx với External LoadBalancer sử dụng vngcloud-controller-manager để tự động expose dịch vụ tới internet bằng bộ cân bằng tải L4 sử dụng một NLB có sẵn với ID = lb-2b9d8974-3760-4d60-8203-9671f229fb96

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: external-http-nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
kind: Service
apiVersion: v1
metadata:
  name: external-http-nginx-service
  annotations:
    vks.vngcloud.vn/package-id: "lbp-ddbf9313-3f4c-471b-afd5-f6a3305159fc"  # ID of the load balancer package
    vks.vngcloud.vn/load-balancer-id: "lb-2b9d8974-3760-4d60-8203-9671f229fb96"
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 80
```

#### **3.Sau khi một NLB mới đã được chúng tôi tự động khởi tạo**, lúc này bạn có thể thực hiện <a href="#integratewithnetworkloadbalancer-3.saukhimotnlbmoidaduocchungtoitudongkhoitao-lucnaybancothethuchien" id="integratewithnetworkloadbalancer-3.saukhimotnlbmoidaduocchungtoitudongkhoitao-lucnaybancothethuchien"></a>

* Chỉnh sửa cấu hình NLB của bạn theo hướng dẫn cụ thể tại [Configure for a Network Load Balancer](https://docs.vngcloud.vn/display/VKSVI/Configure+for+a+Network+Load+Balancer). Ví dụ như bên dưới, tôi đã thực hiện chỉnh sửa protocol và port như sau:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: http-apache2-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: apache2
  template:
    metadata:
      labels:
        app: apache2
    spec:
      containers:
        - name: apache2
          image: httpd
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: http-apache2-service
  annotations:
    vks.vngcloud.vn/load-balancer-id: "lb-f8c0d85b-cb0c-4c77-b382-37982c4d98af"
spec:
  selector:
    app: apache2
  type: LoadBalancer
  ports:
    - name: http
      protocol: TCP
      port: 8000
      targetPort: 80
```

* Cũng giống như các tài nguyên Kubernetes khác, **vngcloud-controller-manager** có cấu trúc gồm các trường thông tin như sau:
  * **apiVersion:** Phiên bản API cho Ingress.
  * **kind:** Loại tài nguyên, trong trường hợp này là "Service".
  * **metadata:** Thông tin mô tả Ingress, bao gồm tên, annotations.
  * **spec:** Cấu hình điều kiện của các incoming request.

Để biết thông tin chung về cách làm việc với **vngcloud-controller-manager,**, hãy xem tại \[Configure for a Network Load Balancer]

* Deploy Service này bằng lệch:

```
kubectl apply -f nginx-service-lb4.yaml
```

***

### Chặn truy cập bằng ACL annotation <a href="#integratewithnetworkloadbalancer-chantruycapbangaclannotation" id="integratewithnetworkloadbalancer-chantruycapbangaclannotation"></a>

Từ phiên bản **0.4.2** của GreenNode LoadBalancer Controller, bạn có thể cấu hình ACL cho listener của Load Balancer ngay trên manifest (Service hoặc Ingress) mà không cần vào vLB Portal. ACL được đánh giá theo thứ tự: **Dropped CIDRs** → **Allowed CIDRs** → **Default action**; rule khớp đầu tiên sẽ thắng, IP không khớp rule nào sẽ áp dụng **Default action**. Lưu lượng bị drop sẽ bị bỏ gói im lặng, client sẽ thấy timeout thay vì lỗi từ chối kết nối.

Các annotation liên quan:

* `vks.vngcloud.vn/dropped-cidrs`: danh sách CIDR/IP IPv4 bị chặn, ngăn cách bằng dấu phẩy.
* `vks.vngcloud.vn/inbound-cidrs`: danh sách CIDR/IP IPv4 được phép truy cập.
* `vks.vngcloud.vn/acl-default-action`: `accept` hoặc `drop`.

Ví dụ: chặn 2 nguồn truy cập vào Service nginx ở trên bằng cách thêm annotation vào Service:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  annotations:
    vks.vngcloud.vn/dropped-cidrs: "203.0.113.9/32,198.51.100.0/24"
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Sau đó chạy lại lệnh `kubectl apply -f nginx-service-lb4.yaml` để cập nhật Service.

Một số trường hợp thường dùng:

| Mục đích | Annotation |
|---|---|
| Chặn một số IP/dải IP | `vks.vngcloud.vn/dropped-cidrs: "203.0.113.9/32,198.51.100.0/24"` |
| Chỉ cho phép một số dải IP | `vks.vngcloud.vn/inbound-cidrs: "192.0.2.0/24"` |
| Cho phép một dải, trừ vài IP | `vks.vngcloud.vn/inbound-cidrs: "192.0.2.0/24"` và `vks.vngcloud.vn/dropped-cidrs: "192.0.2.13/32"` |
| Chặn toàn bộ truy cập | `vks.vngcloud.vn/dropped-cidrs: "0.0.0.0/0"` |

{% hint style="info" %}
Lưu ý:

* ACL annotation chỉ hỗ trợ IPv4 và yêu cầu GreenNode LoadBalancer Controller từ phiên bản **0.4.2**. Các annotation này cũng dùng được cho Ingress (ALB).
* Nếu đặt `inbound-cidrs` mà không đặt `acl-default-action`, default action sẽ là `drop` — chỉ các dải IP đã khai báo được truy cập.
* Giá trị không hợp lệ (CIDR sai định dạng, IPv6, default action lạ) sẽ khiến Service/Ingress báo lỗi và ACL giữ nguyên cấu hình hợp lệ trước đó.
* Gỡ annotation sẽ trả listener về giá trị ACL trước khi LBC quản lý.
* Nếu nhiều Service/Ingress dùng chung một Load Balancer, hãy khai báo ACL nhất quán; nếu không, controller sẽ báo lỗi xung đột.
{% endhint %}

Chi tiết ý nghĩa từng annotation, xem tại [Cấu hình cho một Network Load Balancer](cau-hinh-cho-mot-network-load-balancer.md).

***

### **Kiểm tra thông tin Deployment, Service vừa deploy**

* Chạy câu lệnh sau đây để kiểm tra **Deployment**

```
kubectl get svc,deploy,pod -owide
```

* Nếu kết quả trả về như bên dưới tức là bạn đã deploy Deployment thành công.

```
NAME                    TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE     SELECTOR
service/kubernetes      ClusterIP      10.96.0.1      <none>        443/TCP        5h15m   <none>
service/nginx-service   LoadBalancer   10.96.74.154   <pending>     80:31623/TCP   2s      app=nginx

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
deployment.apps/nginx-app   0/1     1            0           2s    nginx        nginx:1.19.1   app=nginx

NAME                             READY   STATUS              RESTARTS   AGE   IP       NODE                                            NOMINATED NODE   READINESS GATES
pod/nginx-app-7f45b65946-bmrcf   0/1     ContainerCreating   0          2s    <none>   ng-e0fc7245-0c6e-4336-abcc-31a70eeed71d-46179   <none>           <non
```

Lúc này, hệ thống vLB sẽ tự động tạo một LB tương ứng cho nginx app đã deployment, ví dụ:

<figure><img src="../../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

**Bước 3: Để truy cập vào app nginx vừa export, bạn có thể sử dụng URL với định dạng:**

```
http://Endpoint/
```

Bạn có thể lấy thông tin Public Endpoint của Load Balancer tại giao diện vLB. Cụ thể truy cập tại [https://hcm-3.console.greennode.ai/vserver/load-balancer/vlb/](https://hcm-3.console.greennode.ai/vserver/load-balancer/vlb/detail/lb-927c0b5f-5bcf-4ee1-b645-41d6a0caeecb)

Ví dụ, bên dưới tôi đã truy cập thành công vào app nginx với địa chỉ : [http://180.93.181.20/](http://180.93.181.20/)

<figure><img src="../../../.gitbook/assets/expose_l4.png" alt=""><figcaption></figcaption></figure>
