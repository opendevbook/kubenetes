# Install Dasboard

1. Install the Kubernetes Dashboard
First, apply the official Kubernetes Dashboard YAML file:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

```
![](../assets/images/k8s_dashboard.png)

2. Create a Service Account and Bind the ClusterRole
To access the dashboard, you'll need a service account with appropriate permissions:

To access the dashboard, you'll need a service account with appropriate permissions:

```
kubectl create serviceaccount dashboard-admin-sa
kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa

```

3. Get the Access Token
Retrieve the token required to log in to the Kubernetes Dashboard:

