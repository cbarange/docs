# Integrate with Network Load Balancer

To integrate a Network Load Balancer with a Kubernetes cluster, you can use a Service with type [LoadBalancer](loadbalancerhttps://www.airplane.dev/blog/kubernetes-load-balancer) . When you create such a Service, GreenNode LoadBalancer Controller will automatically create an NLB to forward traffic to pods on your [node](nhttps://www.airplane.dev/blog/kubernetes-load-balancer) . You can also use annotations to customize Network Load Balancer properties, such as port, protocol,...

## Prepare <a href="#integratewithnetworkloadbalancer-chuanbi" id="integratewithnetworkloadbalancer-chuanbi"></a>

* Create a Kubernetes cluster on GreenNode, or use an existing cluster. Note: make sure you have downloaded the cluster configuration file once the cluster has been successfully initialized and accessed your cluster.
* Create or use a **service account** created on IAM and attach policy: **vLBFullAccess** , **vServerFullAccess** . To create a service account, go here [and](https://iam.console.greennode.ai/service-accounts) follow these steps:
  * Select " **Create a Service Account** ", enter a name for the Service Account and click **Next Step** to assign permissions to the Service Account
  * Find and select **Policy: vLBFullAccess and Policy: vServerFullAccess** , then click " **Create a Service Account** " to create Service Account, Policy: vLBFullAccess and Policy: vServerFullAccess created by GreenNode, you cannot delete these policies.
  * After successful creation, you need to save **the Client\_ID** and **Secret\_Key** of the Service Account to perform the next step.

***

## Create Service Account and install GreenNode LoadBalancer Controller <a href="#exposemotservicethongquavlblayer4-khoitaoserviceaccountvacaidatvngcloudcontrollermanager" id="exposemotservicethongquavlblayer4-khoitaoserviceaccountvacaidatvngcloudcontrollermanager"></a>

{% hint style="info" %}
**Attention:**

When you initialize the Cluster according to the instructions above, if you have not enabled the **Enable vLB Native Integration Driver** option , by default we will not pre-install this plugin into your Cluster. You need to manually create Service Account and install GreenNode LoadBalancer Controller according to the instructions below. If you have enabled the **Enable vLB Native Integration Driver** option , then we have pre-installed this plugin into your Cluster, skip the Service Account Initialization step, install GreenNode LoadBalancer Controller and continue following the instructions from Deploy once. Workload.
{% endhint %}

<details>

<summary>Instructions for creating Service Account and installing GreenNode LoadBalancer Controller</summary>

**Initialize Service Account**

* Create or use a **service account** created on IAM and attach policy: **vLBFullAccess** , **vServerFullAccess** . To create a service account, go here [and](https://iam.console.greennode.ai/service-accounts) follow these steps:
  * Select " **Create a Service Account** ", enter a name for the Service Account and click **Next Step** to assign permissions to the Service Account
  * Find and select **Policy: vLBFullAccess and Policy: vServerFullAccess** , then click " **Create a Service Account** " to create Service Account, Policy: vLBFullAccess and Policy: vServerFullAccess created by GreenNode, you cannot delete these policies.
  * After successful creation, you need to save **the Client\_ID** and **Secret\_Key** of the Service Account to perform the next step.
* Uninstall cloud-controller-manager

```
kubectl get daemonset -n kube-system | grep -i "cloud-controller-manager"

# if your output is similar to the following, you MUST delete the old plugin
kubectl delete daemonset cloud-controller-manager -n kube-system --force
```

* Besides, you can delete the Service Account being used for the cloud-controller-manager you just removed

```
kubectl get sa -n kube-system | grep -i "cloud-controller-manager"

# if your output is similar to the above, you MUST delete this service account
kubectl delete sa cloud-controller-manager -n kube-system --force
```

**Install GreenNode LoadBalancer Controller**

* Install Helm version 3.0 or higher. Refer to [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/) for instructions on how to install.
* Add this repo to your cluster via the command:

```
helm repo add vks-helm-charts https://vngcloud.github.io/vks-helm-charts
helm repo update
```

* Replace your K8S cluster's ClientID, Client Secret, and ClusterID information and continue running:

```
helm install  vngcloud-controller-manager vks-helm-charts/vngcloud-controller-manager --replace \
  --namespace kube-system \
  --set cloudConfig.global.clientID= <Lấy ClientID của Service Account được tạo trên IAM theo hướng dẫn bên trên> \
  --set cloudConfig.global.clientSecret= <Lấy ClientSecret của Service Account được tạo trên IAM theo hướng dẫn bên trên>\
  --set cluster.clusterID= <Lấy Cluster ID của cluster mà bạn đã khởi tạo trước đó>
```

* After the installation is complete, check the status of vngcloud-Integrate-controller pods:

```
kubectl get pods -n kube-system | grep vngcloud-controller-manager
```

For example, in the image below you have successfully installed vngcloud-controller-manager:

```
NAME                                          READY   STATUS    RESTARTS      AGE
vngcloud-controller-manager-8864c754c-bqhvz   1/1     Running   5 (91s ago)   3m13sc
```

</details>

***

## Deploy a Workload <a href="#exposemotservicethongquavlblayer4-deploymotworkload" id="exposemotservicethongquavlblayer4-deploymotworkload"></a>

**1.If you do not have a previously initialized Network Load Balancer available on the vLB system.**

At this point, you need to do:

### **Step 1** : **Create Deployment, Service for Nginx app.**

* Create **nginx-service-lb4.yaml** file with the following content:

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

* Or use the following script file to deploy HTTP Apache Service with Internal LoadBalancer allowing internal access on port 8080:

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

* Or sample YAML file to create Deployment and Service for a UDP server application in a Kubernetes cluster:

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

**2.If you already have a previously initialized Network Load Balancer on the vLB system and you want to reuse the NLB for your cluster.**

At this point, please enter the Load Balancer ID information into the **vks.vngcloud.vn/load-balancer-id annotation.** The example below is a sample YAML file to deploy Nginx with External LoadBalancer using vngcloud-controller-manager to automatically expose the service to the internet using an L4 load balancer using an available NLB with ID = lb-2b9d8974- 3760-4d60-8203-9671f229fb96

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

**3.Once a new NLB has been automatically created by us , you can now proceed**

* Edit your NLB configuration according to the specific instructions at [Configure for a Network Load Balancer](https://docs.vngcloud.vn/display/VKSVI/Configure+for+a+Network+Load+Balancer) . For example below, I have edited the protocol and port as follows:

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

* Like other Kubernetes resources, **vngcloud-controller-manager** has a structure including the following information fields:
  * **apiVersion:** API version for Ingress.
  * **kind:** Resource type, in this case "Service".
  * **metadata:** Information describing Ingress, including name, annotations.
  * **spec:** Configure the conditions of incoming requests.

For general information about working with **vngcloud-controller-manager,** see \[Configure for a Network Load Balancer]

* Deploy this Service using:

```
kubectl apply -f nginx-service-lb4.yaml
```

***

### Block access with ACL annotations <a href="#integratewithnetworkloadbalancer-blockaccesswithaclannotations" id="integratewithnetworkloadbalancer-blockaccesswithaclannotations"></a>

Starting with version **0.4.2** of GreenNode LoadBalancer Controller, you can configure the ACL of a Load Balancer listener directly in your manifest (Service or Ingress) without using the vLB Portal. ACL rules are evaluated in the following order: **Dropped CIDRs** → **Allowed CIDRs** → **Default action**; the first matching rule wins, and any IP that matches no rule gets the **Default action**. Dropped traffic is silently discarded, so the client sees a timeout instead of a connection refusal.

Related annotations:

* `vks.vngcloud.vn/dropped-cidrs`: comma-separated list of blocked IPv4 CIDRs/IPs.
* `vks.vngcloud.vn/inbound-cidrs`: comma-separated list of allowed IPv4 CIDRs/IPs.
* `vks.vngcloud.vn/acl-default-action`: `accept` or `drop`.

For example, block two sources from accessing the nginx Service above by adding this annotation to the Service:

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

Then run `kubectl apply -f nginx-service-lb4.yaml` again to update the Service.

Common use cases:

| Goal | Annotation |
|---|---|
| Block specific IPs/ranges | `vks.vngcloud.vn/dropped-cidrs: "203.0.113.9/32,198.51.100.0/24"` |
| Allow only specific IP ranges | `vks.vngcloud.vn/inbound-cidrs: "192.0.2.0/24"` |
| Allow a range except a few IPs | `vks.vngcloud.vn/inbound-cidrs: "192.0.2.0/24"` and `vks.vngcloud.vn/dropped-cidrs: "192.0.2.13/32"` |
| Block all access | `vks.vngcloud.vn/dropped-cidrs: "0.0.0.0/0"` |

{% hint style="info" %}
Notes:

* ACL annotations support IPv4 only and require GreenNode LoadBalancer Controller **0.4.2** or later. They also work for Ingress (ALB).
* If you set `inbound-cidrs` without `acl-default-action`, the default action is `drop` — only the declared IP ranges can access the listener.
* Invalid values (malformed CIDR, IPv6, unknown default action) make the Service/Ingress report an error, and the ACL keeps the last valid configuration.
* Removing the annotation restores the listener ACL to the value it had before the controller took over.
* If multiple Services/Ingresses share one Load Balancer, declare the ACL consistently; otherwise the controller reports a conflict.
{% endhint %}

For the full meaning of each annotation, see [Configure for a Network Load Balancer](cau-hinh-cho-mot-network-load-balancer.md).

***

### **Step 2: Check Deployment and Service information just deployed** <a href="#kiem-tra-thong-tin-deployment-service-vua-deploy" id="kiem-tra-thong-tin-deployment-service-vua-deploy"></a>

* Run the following command to test **Deployment**

```
kubectl get svc,deploy,pod -owide
```

* If the results are returned as below, it means you have deployed Deployment successfully.

```
NAME                    TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE     SELECTOR
service/kubernetes      ClusterIP      10.96.0.1      <none>        443/TCP        5h15m   <none>
service/nginx-service   LoadBalancer   10.96.74.154   <pending>     80:31623/TCP   2s      app=nginx

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
deployment.apps/nginx-app   0/1     1            0           2s    nginx        nginx:1.19.1   app=nginx

NAME                             READY   STATUS              RESTARTS   AGE   IP       NODE                                            NOMINATED NODE   READINESS GATES
pod/nginx-app-7f45b65946-bmrcf   0/1     ContainerCreating   0          2s    <none>   ng-e0fc7245-0c6e-4336-abcc-31a70eeed71d-46179   <none>           <non
```

At this point, the vLB system will automatically create a corresponding LB for the deployed nginx app, for example:

<figure><img src="https://docs.vngcloud.vn/~gitbook/image?url=https%3A%2F%2F3672463924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FB0NrrrdJdpYOYzRkbWp5%252Fuploads%252FzD11c32iEIzE2hnbzbrv%252Fimage.png%3Falt%3Dmedia%26token%3D8010e3b8-fb5b-428a-9940-e7ff245895d2&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=58607080&#x26;sv=1" alt=""><figcaption></figcaption></figure>

### **Step 3: To access the just exported nginx app, you can use the URL with the format:**

```
http://Endpoint/
```

You can get Load Balancer Public Endpoint information at the vLB interface. Specifically, access at [https://hcm-3.console.greennode.ai/vserver/load-balancer/vlb/](https://hcm-3.console.greennode.ai/vserver/load-balancer/vlb/detail/lb-927c0b5f-5bcf-4ee1-b645-41d6a0caeecb)

For example, below I have successfully accessed the nginx app with the address: [http://180.93.181.20/](http://180.93.181.20/)

<figure><img src="https://docs.vngcloud.vn/~gitbook/image?url=https%3A%2F%2F3672463924-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FB0NrrrdJdpYOYzRkbWp5%252Fuploads%252FWRLfY9NlcRgvi7B2c3b7%252Fimage.png%3Falt%3Dmedia%26token%3D9aea9889-1f6f-478a-889e-8b936c2b3bb6&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b1f9d46b&#x26;sv=1" alt=""><figcaption></figcaption></figure>
