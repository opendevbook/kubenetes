# Deploy first deployment

- Run on Master node
```
vagrant ssh k8s-master-01
```

create project folder
```
[vagrant@k8s-master-01 ~]$ mkdir controller
[vagrant@k8s-master-01 ~]$ cd controller
[vagrant@k8s-master-01 ~]$ vim nginx-deployment.yml
```

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

- Create a Deployment based on the YAML file:
```
[vagrant@k8s-master-01 controller]$ kubectl apply -f nginx-deployment.yml
deployment.apps/nginx-deployment created
```

- Display information about the Deployment:
```
kubectl describe deployment nginx-deployment
```

![](../assets/images/first_deploy2.png)

Run ```kubectl get deployments``` to check if the Deployment was created.

If the Deployment is still being created, the output is similar to the following:

```
[vagrant@k8s-master-01 controller]$ kubectl get deployments
```


When you inspect the Deployments in your cluster, the following fields are displayed:

- NAME lists the names of the Deployments in the namespace.
- READY displays how many replicas of the application are available to your users. It follows the pattern ready/desired.
- UP-TO-DATE displays the number of replicas that have been updated to achieve the desired state.
- AVAILABLE displays how many replicas of the application are available to your users.
- AGE displays the amount of time that the application has been running.

**Notice** how the number of desired replicas is 3 according to .spec.replicas field.

- To see the ReplicaSet (rs) created by the Deployment, run kubectl get rs. The output is similar to this:

```
[vagrant@k8s-master-01 controller]$ kubectl get rs
```

ReplicaSet output shows the following fields:

- NAME lists the names of the ReplicaSets in the namespace.
- DESIRED displays the desired number of replicas of the application, which you define when you create the Deployment. This is the desired state.
- CURRENT displays how many replicas are currently running.
- READY displays how many replicas of the application are available to your users.
- AGE displays the amount of time that the application has been running.-

- To see the labels automatically generated for each Pod, run kubectl get pods --show-labels. The output is similar to:

```
[vagrant@k8s-master-01 controller]$ kubectl get pods --show-labels

```



-Running get pods should now show only the new Pods  ```kubctl get pods```
```
[vagrant@k8s-master-01 controller]$ kubectl get pods 
```

- To see network  run ```kubectl get services```

```
[vagrant@k8s-master-01 ~]$ kubectl get services
```

- Delete deployment
```
[vagrant@k8s-master-01 ~]$ kubectl delete deployments.apps nginx-deployment 
deployment.apps "nginx-deployment" deleted
[vagrant@k8s-master-01 ~]$ kubectl delete pods  --all
No resources found
```