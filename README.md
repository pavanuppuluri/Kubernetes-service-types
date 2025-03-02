# Understanding Kubernetes Service Types and Their Use Cases

- Kubernetes provides **four main types of services** to expose applications running in a cluster.
- Each service type is designed for different use cases.



## **1️⃣ ClusterIP (Default)**
- **Description**: Exposes the service **internally within the cluster**.  
- **Use Case**: When you want communication between pods within the cluster but do not need external access.  
- **Example YAML:**
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 8080
  ```

**Access**
```
kubectl exec -it <pod-name> -- curl http://my-service
```

## **2️⃣ NodePort**
- **Description**: Exposes the service on a static port across all nodes in the cluster.
- **Use Case**: When you need external access but without a load balancer.
- **Example YAML**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30007
```

**Access (from outside the cluster):**
```
curl http://<node-ip>:30007
```

## **3️⃣ LoadBalancer**
- **Description**: Provisions an external load balancer that routes traffic to the service.
- **Use Case**: When you need to expose the service publicly on the internet.
- **Example YAML**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

### Access: Get the external IP using:

```sh
kubectl get svc my-service
```

**Example output**

| NAME       | TYPE         | CLUSTER-IP     | EXTERNAL-IP  | PORT(S)       |
|------------|-------------|---------------|--------------|--------------|
| my-service | LoadBalancer | 10.100.200.1  | 34.90.12.34  | 80:32123/TCP |


**Then, access it via:**
```
curl http://34.90.12.34
```

## **4️⃣ ExternalName**
- **Description**: Maps the service to an external domain name (DNS).
- **Use Case**: When you need to access an external service (e.g., database or API) using a Kubernetes service.
- **Example YAML**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-service
spec:
  type: ExternalName
  externalName: api.external.com
```

**Access**:
```
nslookup external-service.default.svc.cluster.local
```

This will resolve to api.external.com.

### Summary

| Service Type   | Exposes Service              | Access From                         | Use Case                        |
|---------------|-----------------------------|-------------------------------------|---------------------------------|
| ClusterIP     | Internally in the cluster   | Pods within cluster                | Internal communication         |
| NodePort      | Exposes on node IP & port   | External clients via `<NodeIP>:<NodePort>` | Basic external access      |
| LoadBalancer  | External load balancer      | Public Internet                    | Full external access           |
| ExternalName  | Maps to an external DNS     | Kubernetes DNS lookup              | External service integration   |

