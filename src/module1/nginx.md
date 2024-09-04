# nginx

- nginx-deployment.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3  # Number of replicas/pods
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
        image: nginx:latest  # Image to use
        ports:
        - containerPort: 80  # Port exposed by the container

```

- nginx-service.yml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80  # Port exposed by the service
      targetPort: 80  # Port the container listens on
  type: LoadBalancer  # Expose the service externally (for cloud providers)

```

- Command
```
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml

kubectl get deployments
kubectl get pods
kubectl get services
```

- scan up/down replica
```
kubectl scale deployment nginx-deployment --replicas=5
```

- Verify the Change
```
kubectl get deployment nginx-deployment
```
- Clean
```
kubectl delete deployment nginx-deployment
kubectl delete pods --all
```