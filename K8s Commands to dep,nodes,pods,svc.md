
# Kubernetes Guide: Create Nodes, Pods, Services, and Deployments

## 1. Set Up Minikube
Start Minikube, which acts as a single-node Kubernetes cluster:
```bash
minikube start
```

Check Minikube status:
```bash
minikube status
```

Get the Minikube cluster IP:
```bash
minikube ip
```

---

## 2. Create a Node (Optional)
Kubernetes nodes are part of the cluster by default (in Minikube, there's one node). Additional nodes require setting up a multi-node cluster, which is outside Minikube’s typical use. Skip this step unless you're managing a custom cluster.

---

## 3. Create a Pod
Pods are the basic deployable unit in Kubernetes.

### Command:
```bash
kubectl run <pod-name> --image=<image-name> --restart=Never
```

### Example:
```bash
kubectl run hello-node --image=nginx --restart=Never
```

### Verify Pod Creation:
```bash
kubectl get pods
```

---

## 4. Create a Deployment
Deployments manage a group of pods and ensure desired state.

### Command:
```bash
kubectl create deployment <deployment-name> --image=<image-name>
```

### Example:
```bash
kubectl create deployment nginx-deployment --image=nginx
```

### Verify Deployment:
```bash
kubectl get deployments
```

### Scale Deployment (Optional):
```bash
kubectl scale deployment <deployment-name> --replicas=<number>
```
Example:
```bash
kubectl scale deployment nginx-deployment --replicas=2
```

---

## 5. Create a Service
Services expose your pods to external or internal traffic.

### ClusterIP Service (Internal Communication):
```bash
kubectl expose deployment <deployment-name> --type=ClusterIP --port=<port>
```
Example:
```bash
kubectl expose deployment nginx-deployment --type=ClusterIP --port=80
```

### NodePort Service (External Communication):
```bash
kubectl expose deployment <deployment-name> --type=NodePort --port=<port> --target-port=<target-port>
```
Example:
```bash
kubectl expose deployment nginx-deployment --type=NodePort --port=80 --target-port=80
```

### Check Services:
```bash
kubectl get svc
```

### Access NodePort Service:
Use the Minikube IP and NodePort to access:
```
http://<minikube-ip>:<nodeport>
```

---

## 6. Deploy Using Manifest Files
Use YAML manifest files to create Kubernetes resources.

### Example YAML:

`pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-node
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

`service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: NodePort
```

### Apply YAML Files:
```bash
kubectl apply -f pod.yaml
kubectl apply -f service.yaml
```

---

## 7. Other Useful Commands
### View All Resources:
```bash
kubectl get all
```

### Check Logs for a Pod:
```bash
kubectl logs <pod-name>
```

### Describe Resources:
```bash
kubectl describe <resource-type> <resource-name>
```
Example:
```bash
kubectl describe pod hello-node
```

### Delete a Resource:
```bash
kubectl delete <resource-type> <resource-name>
```
Example:
```bash
kubectl delete pod hello-node
```

### Clean Up All Resources:
```bash
kubectl delete all --all
```

---

## 8. Verify Accessibility
Use the Minikube IP and NodePort (from `kubectl get svc`) to test services in your browser:
```
http://<minikube-ip>:<nodeport>
```
Example:
```
http://192.168.49.2:30080
```

---

This guide covers creating nodes, pods, services, deployments, and exposing services for external access in Kubernetes. Let me know if you need help with specific YAML configurations or troubleshooting.
