#  Basic Deployment 
![](../assets/images/logo-kube.jpg)

Beginners
A basic Kubernetes lab setup for beginners to understand how to deploy, scale, and manage applications in a Kubernetes cluster. In this example, we'll deploy an Nginx web server.  

**Step 1. Prerequisites**
Ensure you have the following:

Kubernetes Cluster: Use Minikube, k3s, or a cloud-based Kubernetes cluster.
kubectl: Command-line tool to interact with your Kubernetes cluster.
```
cd ~
mkdir basic
cd basic
```

```
[vagrant@k8s-master-01 basic]$ kubectl  get nodes -o wide
NAME            STATUS   ROLES           AGE     VERSION    INTERNAL-IP     EXTERNAL-IP   OS-IMAGE          KERNEL-VERSION          CONTAINER-RUNTIME
k8s-master-01   Ready    control-plane   6d21h   v1.28.13   192.168.35.10   <none>        CentOS Stream 9   5.14.0-503.el9.x86_64   containerd://1.7.21
k8s-node-01     Ready    <none>          91m     v1.28.13   192.168.35.21   <none>        CentOS Stream 9   5.14.0-503.el9.x86_64   containerd://1.7.21
k8s-node-02     Ready    <none>          85m     v1.28.13   192.168.35.22   <none>        CentOS Stream 9   5.14.0-503.el9.x86_64   containerd://1.7.21
k8s-node-03     Ready    <none>          83m     v1.28.13   192.168.35.23   <none>        CentOS Stream 9   5.14.0-503.el9.x86_64   containerd://1.7.21
```

**Step 2. Create a Namespace**
Namespaces are used to logically separate resources within a Kubernetes cluster. ```kubectl create namespace my-lab```
```
[vagrant@k8s-master-01 ~]$ kubectl create namespace my-lab
namespace/my-lab created
```

**Step 3. Deploy an Nginx Application**
We'll create a deployment resource for Nginx, which is a simple web server.

- 3.1 **Create a Deployment YAML File**
Create a file called nginx-deployment.yaml:
```
cat << EOF | tee nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: my-lab
  labels:
    app: nginx
spec:
  replicas: 3
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
        image: nginx:1.17
        ports:
        - containerPort: 80
EOF
```

- 3.2 **Apply the Deployment**
Run the following command to create the Nginx deployment:  ```kubectl apply -f nginx-deployment.yaml```

```
[vagrant@k8s-master-01 basic]$ kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment created
```

Verify
```
[vagrant@k8s-master-01 basic]$ kubectl get deployments -n my-lab
[vagrant@k8s-master-01 basic]$ kubectl get pods -n my-lab
```

![](../assets/images/basic_nginx_deploy.png)

**Step 4. Expose the Nginx Application**
Create a Service to expose the Nginx application.

- 4.1 Create a Service YAML File
Create a file called nginx-service.yaml:

```
cat <<EOF |  tee nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: my-lab
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort

EOF
```

- 4.2 Apply the Service
Run the following command to create the service: ```kubectl apply -f nginx-service.yaml```
```
[vagrant@k8s-master-01 basic]$ kubectl apply -f nginx-service.yaml
service/nginx-service created
```

Check the service:  ```kubectl get services -n my-lab```
```
[vagrant@k8s-master-01 basic]$ kubectl get services -n my-lab
NAME            TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
nginx-service   NodePort   10.100.135.221   <none>        80:32707/TCP   11s
```

- 4.3 Access the Nginx Application
Find the NodePort assigned to your service:

```
[vagrant@k8s-master-01 basic]$ kubectl get svc nginx-service -n my-lab
```

You can now access Nginx using your node’s IP and the assigned port:

```
http://<node-ip>:<node-port>
```

Step 5. Scale the Nginx Deployment
You can scale the deployment to run more replicas of Nginx:

```
[vagrant@k8s-master-01 basic]$ kubectl scale deployment/nginx-deployment --replicas=5 -n my-lab
deployment.apps/nginx-deployment scaled
```

```
[vagrant@k8s-master-01 basic]$ kubectl get pods -n my-lab
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-6b8f6d655f-7k48j   0/1     ContainerCreating   0          10s
nginx-deployment-6b8f6d655f-98fbq   0/1     ContainerCreating   0          10s
nginx-deployment-6b8f6d655f-phlk9   0/1     ContainerCreating   0          6m45s
nginx-deployment-6b8f6d655f-w7w59   0/1     ContainerCreating   0          6m45s
nginx-deployment-6b8f6d655f-zcj9p   0/1     ContainerCreating   0          6m45s
```

**Step 6. View Nginx Logs**
Check the logs of a specific Nginx pod:

```
[vagrant@k8s-master-01 basic]$ kubectl logs <nginx-pod-name> -n my-lab
```

**Step 7. Delete the Resources**
Once you're done with the lab, you can delete the resources:

```
[vagrant@k8s-master-01 basic]$ kubectl delete deployment nginx-deployment -n my-lab
deployment.apps "nginx-deployment" deleted

[vagrant@k8s-master-01 basic]$ kubectl delete service nginx-service -n my-lab
service "nginx-service" deleted

[vagrant@k8s-master-01 basic]$ kubectl delete namespace my-lab
namespace "my-lab" deleted

```

:)

