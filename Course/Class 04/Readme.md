# Class 4: Kubernetes Deployments and Scaling for Python Applications

Welcome to **Class 4** of our Kubernetes course tailored for Python developers! In this session, you'll learn how to manage application deployments, implement scaling strategies, and ensure high availability for your Python applications on Kubernetes. You'll explore Deployments, ReplicaSets, and Horizontal Pod Autoscalers (HPA) to efficiently manage your application's lifecycle and performance. By the end of this class, you'll be equipped to deploy, scale, and maintain robust Python applications within a Kubernetes cluster.

---

## Table of Contents

1. [Objectives](#objectives)
2. [Key Topics](#key-topics)
3. [Step-by-Step Guide](#step-by-step-guide)
4. [Hands-On Demonstration](#hands-on-demonstration)
5. [Resources](#resources)
6. [Assignment](#assignment)
7. [FAQs](#faqs)

---

## Objectives

By the end of this class, you will be able to:

- **Understand** the concepts of Deployments and ReplicaSets in Kubernetes.
- **Deploy** Python applications using Kubernetes Deployments.
- **Implement** scaling strategies to handle varying workloads.
- **Configure** Horizontal Pod Autoscalers (HPA) for automatic scaling based on resource utilization.
- **Ensure** high availability and fault tolerance for your applications.
- **Manage** rolling updates and rollbacks to maintain application uptime during updates.

---

## Key Topics

### 1. Kubernetes Deployments and ReplicaSets

- **Deployments:**
  - Declarative updates for Pods and ReplicaSets.
  - Managing application lifecycle.
  - Rolling updates and rollbacks.
  
- **ReplicaSets:**
  - Ensuring a specified number of pod replicas are running.
  - Replacing failed or terminated Pods.

### 2. Scaling Strategies

- **Manual Scaling:**
  - Adjusting the number of replicas manually.
  
- **Automatic Scaling:**
  - Using Horizontal Pod Autoscalers (HPA) to scale based on metrics.

### 3. Horizontal Pod Autoscalers (HPA)

- **Functionality:**
  - Automatically scaling the number of Pods based on observed CPU utilization or other select metrics.
  
- **Configuration:**
  - Setting up HPA with Deployment manifests.
  - Custom metrics for scaling.

### 4. Ensuring High Availability

- **Best Practices:**
  - Distributing Pods across multiple nodes.
  - Using multiple ReplicaSets.
  
- **Fault Tolerance:**
  - Automatic rescheduling of failed Pods.
  - Ensuring minimal downtime during updates.

### 5. Rolling Updates and Rollbacks

- **Rolling Updates:**
  - Gradually updating Pods to a new version.
  - Minimizing downtime and maintaining service availability.
  
- **Rollbacks:**
  - Reverting to a previous Deployment version in case of failures.
  
---

## Step-by-Step Guide

### Step 1: Understanding Deployments and ReplicaSets

#### **Step 1.1: What is a Deployment?**

**Explanation:**
- A Deployment provides declarative updates to applications. It manages the creation and scaling of ReplicaSets and Pods, ensuring that the desired state of the application is maintained.

#### **Step 1.2: What is a ReplicaSet?**

**Explanation:**
- A ReplicaSet ensures that a specified number of Pod replicas are running at any given time. It replaces Pods that are deleted or fail.

### Step 2: Deploying a Python Flask Application with a Deployment

#### **Step 2.1: Create a Deployment YAML File**

**Where to Run:** In your text editor.

1. **Create `flask-deployment.yaml`:**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: flask-deployment
     labels:
       app: flask-app
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: flask-app
     template:
       metadata:
         labels:
           app: flask-app
       spec:
         containers:
         - name: flask-container
           image: your-dockerhub-username/flask-app:latest
           ports:
           - containerPort: 5000
           env:
           - name: DATABASE_HOST
             value: "db-service"
           - name: DATABASE_USER
             valueFrom:
               secretKeyRef:
                 name: db-secret
                 key: username
           - name: DATABASE_PASSWORD
             valueFrom:
               secretKeyRef:
                 name: db-secret
                 key: password
           envFrom:
           - configMapRef:
               name: app-config
           resources:
             requests:
               cpu: "250m"
               memory: "256Mi"
             limits:
               cpu: "500m"
               memory: "512Mi"
   ```
   - **Explanation:**
     - **replicas:** Number of desired Pod replicas.
     - **selector:** Labels to identify Pods managed by this Deployment.
     - **template:** Pod specification including containers, ports, environment variables, and resource requests/limits.

2. **Apply the Deployment:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

#### **Step 2.2: Verify the Deployment**

**Where to Run:** In your terminal or command prompt.

1. **Check Deployment Status:**
   ```bash
   kubectl get deployments
   ```
   - **Expected Output:**
     ```
     NAME              READY   UP-TO-DATE   AVAILABLE   AGE
     flask-deployment  2/2     2            2           5m
     ```

2. **Check ReplicaSets:**
   ```bash
   kubectl get replicasets
   ```
   - **Expected Output:**
     ```
     NAME                                 DESIRED   CURRENT   READY   AGE
     flask-deployment-6d4b8f5d8b         2         2         2       5m
     ```

3. **Check Pods:**
   ```bash
   kubectl get pods
   ```
   - **Expected Output:**
     ```
     NAME                                READY   STATUS    RESTARTS   AGE
     flask-deployment-6d4b8f5d8b-abcde   1/1     Running   0          5m
     flask-deployment-6d4b8f5d8b-fghij   1/1     Running   0          5m
     ```

### Step 3: Exposing the Deployment via a Service

#### **Step 3.1: Create a Service YAML File**

**Where to Run:** In your text editor.

1. **Create `flask-service.yaml`:**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: flask-service
   spec:
     type: NodePort
     selector:
       app: flask-app
     ports:
       - protocol: TCP
         port: 5000
         targetPort: 5000
         nodePort: 30001
   ```
   - **Explanation:**
     - **type: NodePort:** Exposes the service on each node’s IP at a static port (e.g., 30001).
     - **selector:** Targets Pods with the label `app: flask-app`.
     - **ports:** Maps port 5000 of the service to port 5000 of the Pods and exposes it on node port 30001.

2. **Apply the Service:**
   ```bash
   kubectl apply -f flask-service.yaml
   ```

#### **Step 3.2: Verify the Service**

**Where to Run:** In your terminal or command prompt.

1. **Check Service Status:**
   ```bash
   kubectl get services
   ```
   - **Expected Output:**
     ```
     NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
     flask-service   NodePort    10.96.0.1       <none>        5000:30001/TCP   5m
     kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP          15m
     ```

2. **Access the Flask Application:**
   - **Retrieve Minikube IP:**
     ```bash
     minikube ip
     ```
     - **Example Output:** `192.168.99.100`
   
   - **Navigate to Flask App:**
     - Open your web browser and go to `http://192.168.99.100:30001`.
     - **Expected Output:** The Flask application's welcome page or functionality.

### Step 4: Scaling the Deployment

#### **Step 4.1: Manual Scaling**

**Where to Run:** In your terminal or command prompt.

1. **Scale Deployment to 3 Replicas:**
   ```bash
   kubectl scale deployment flask-deployment --replicas=3
   ```
   
2. **Verify Scaling:**
   ```bash
   kubectl get deployments
   ```
   - **Expected Output:**
     ```
     NAME              READY   UP-TO-DATE   AVAILABLE   AGE
     flask-deployment  3/3     3            3           10m
     ```
   
3. **Check Pods:**
   ```bash
   kubectl get pods
   ```
   - **Expected Output:**
     ```
     NAME                                READY   STATUS    RESTARTS   AGE
     flask-deployment-6d4b8f5d8b-abcde   1/1     Running   0          10m
     flask-deployment-6d4b8f5d8b-fghij   1/1     Running   0          10m
     flask-deployment-6d4b8f5d8b-klmno   1/1     Running   0          10m
     ```

#### **Step 4.2: Implementing Horizontal Pod Autoscaler (HPA)**

**Where to Run:** In your terminal or command prompt.

1. **Ensure Metrics Server is Installed:**
   - **Check if Metrics Server is Running:**
     ```bash
     kubectl get deployment metrics-server -n kube-system
     ```
   - **If Not Installed, Install Metrics Server:**
     ```bash
     kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
     ```

2. **Create an HPA for the Deployment:**
   ```bash
   kubectl autoscale deployment flask-deployment --cpu-percent=50 --min=2 --max=5
   ```
   - **Explanation:**
     - **--cpu-percent=50:** Target average CPU utilization across all Pods.
     - **--min=2:** Minimum number of replicas.
     - **--max=5:** Maximum number of replicas.

3. **Verify HPA Configuration:**
   ```bash
   kubectl get hpa
   ```
   - **Expected Output:**
     ```
     NAME              REFERENCE                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
     flask-deployment  Deployment/flask-deployment   30%/50%   2         5         3          5m
     ```

4. **Simulate Load to Test HPA:**
   - **Use a Load Testing Tool (e.g., Apache Benchmark):**
     ```bash
     ab -n 1000 -c 100 http://192.168.99.100:30001/
     ```
   - **Monitor HPA Scaling:**
     ```bash
     kubectl get hpa
     ```
     - **Observe the number of replicas increasing based on CPU usage.**

### Step 5: Rolling Updates and Rollbacks

#### **Step 5.1: Perform a Rolling Update**

**Where to Run:** In your text editor and terminal.

1. **Update the Flask Application Image:**
   - **Modify Deployment YAML (`flask-deployment.yaml`):**
     ```yaml
     spec:
       containers:
       - name: flask-container
         image: your-dockerhub-username/flask-app:v2.0
         ...
     ```
   
2. **Apply the Updated Deployment:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

3. **Monitor the Rolling Update:**
   ```bash
   kubectl rollout status deployment/flask-deployment
   ```
   - **Expected Output:**
     ```
     deployment "flask-deployment" successfully rolled out
     ```

4. **Verify the Update:**
   ```bash
   kubectl get pods
   ```
   - **Ensure that Pods are running the new image version.**

#### **Step 5.2: Rollback to Previous Deployment**

**Where to Run:** In your terminal or command prompt.

1. **Rollback the Deployment:**
   ```bash
   kubectl rollout undo deployment/flask-deployment
   ```

2. **Monitor the Rollback:**
   ```bash
   kubectl rollout status deployment/flask-deployment
   ```
   - **Expected Output:**
     ```
     deployment "flask-deployment" successfully rolled out
     ```

3. **Verify the Rollback:**
   ```bash
   kubectl get pods
   ```
   - **Ensure that Pods are reverted to the previous image version.**

---

## Hands-On Demonstration

### **Deploying and Scaling a Python Flask Application with HPA**

Follow these steps to deploy a Python Flask application on Kubernetes, implement scaling strategies, and ensure high availability using Horizontal Pod Autoscalers.

#### **Step 1: Deploy the Flask Application**

**Where to Run:** In your text editor and terminal.

1. **Apply Deployment and Service:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   kubectl apply -f flask-service.yaml
   ```

2. **Verify Deployment:**
   ```bash
   kubectl get deployments
   kubectl get services
   kubectl get pods
   ```

3. **Access the Application:**
   - Retrieve Minikube IP:
     ```bash
     minikube ip
     ```
   - Navigate to `http://<minikube-ip>:30001` in your browser.

#### **Step 2: Scale the Deployment Manually**

**Where to Run:** In your terminal or command prompt.

1. **Scale to 3 Replicas:**
   ```bash
   kubectl scale deployment flask-deployment --replicas=3
   ```

2. **Verify Scaling:**
   ```bash
   kubectl get deployments
   kubectl get pods
   ```

3. **Access Each Pod Instance:**
   - All Pods serve the same application; accessing the service URL will route to any available Pod.

#### **Step 3: Implement Horizontal Pod Autoscaler (HPA)**

**Where to Run:** In your terminal or command prompt.

1. **Ensure Metrics Server is Running:**
   ```bash
   kubectl get deployment metrics-server -n kube-system
   ```
   - **If Not Running, Install:**
     ```bash
     kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
     ```

2. **Create HPA:**
   ```bash
   kubectl autoscale deployment flask-deployment --cpu-percent=50 --min=2 --max=5
   ```

3. **Verify HPA:**
   ```bash
   kubectl get hpa
   ```

4. **Simulate Load to Trigger HPA:**
   - **Use Apache Benchmark:**
     ```bash
     ab -n 1000 -c 100 http://<minikube-ip>:30001/
     ```
   - **Monitor HPA Scaling:**
     ```bash
     kubectl get hpa
     ```

5. **Observe the Increase in Replicas:**
   - HPA should automatically scale the number of Pods based on CPU usage.

#### **Step 4: Perform a Rolling Update**

**Where to Run:** In your text editor and terminal.

1. **Update Deployment Image Version:**
   - **Modify `flask-deployment.yaml`:**
     ```yaml
     spec:
       containers:
       - name: flask-container
         image: your-dockerhub-username/flask-app:v2.0
         ...
     ```
   
2. **Apply the Updated Deployment:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

3. **Monitor the Rolling Update:**
   ```bash
   kubectl rollout status deployment/flask-deployment
   ```

4. **Verify the Update:**
   ```bash
   kubectl get pods
   ```

#### **Step 5: Rollback if Needed**

**Where to Run:** In your terminal or command prompt.

1. **Rollback Deployment:**
   ```bash
   kubectl rollout undo deployment/flask-deployment
   ```

2. **Monitor the Rollback:**
   ```bash
   kubectl rollout status deployment/flask-deployment
   ```

3. **Verify the Rollback:**
   ```bash
   kubectl get pods
   ```

#### **Step 6: Clean Up Resources**

**Where to Run:** In your terminal or command prompt.

1. **Delete Service and Deployment:**
   ```bash
   kubectl delete service flask-service
   kubectl delete deployment flask-deployment
   ```

2. **Delete HPA:**
   ```bash
   kubectl delete hpa flask-deployment
   ```

3. **Stop and Delete Minikube Cluster (Optional):**
   ```bash
   minikube stop
   minikube delete
   ```

---

## Resources

- **Kubernetes Official Documentation:** [https://kubernetes.io/docs/home/](https://kubernetes.io/docs/home/)
- **Horizontal Pod Autoscaling:** [https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- **Kubernetes Deployments:** [https://kubernetes.io/docs/concepts/workloads/controllers/deployment/](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- **ReplicaSets:** [https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- **Kubernetes Networking:** [https://kubernetes.io/docs/concepts/services-networking/](https://kubernetes.io/docs/concepts/services-networking/)
- **Kubernetes Storage:** [https://kubernetes.io/docs/concepts/storage/](https://kubernetes.io/docs/concepts/storage/)
- **Metrics Server:** [https://github.com/kubernetes-sigs/metrics-server](https://github.com/kubernetes-sigs/metrics-server)
- **Kubectl Cheat Sheet:** [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- **Prometheus and Grafana Integration:** [https://prometheus.io/docs/prometheus/latest/getting_started/](https://prometheus.io/docs/prometheus/latest/getting_started/)
- **Kubernetes Best Practices:** [https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

---

## Assignment

### **Objective:**

Apply the concepts learned by deploying a scalable Python Flask application on Kubernetes, implementing scaling strategies, and managing rolling updates and rollbacks.

### **Tasks:**

1. **Deploy the Flask Application:**
   - **Where to Run:** In your text editor and terminal.
   - Create and apply a Deployment (`flask-deployment.yaml`) and Service (`flask-service.yaml`) for your Python Flask application.
   - Ensure the application is accessible via the specified NodePort.

2. **Scale the Deployment Manually:**
   - **Where to Run:** In your terminal or command prompt.
   - Scale the Deployment to 3 replicas using:
     ```bash
     kubectl scale deployment flask-deployment --replicas=3
     ```
   - Verify that 3 Pods are running:
     ```bash
     kubectl get pods
     ```

3. **Implement Horizontal Pod Autoscaler (HPA):**
   - **Where to Run:** In your terminal or command prompt.
   - Create an HPA for the Flask Deployment targeting 50% CPU utilization:
     ```bash
     kubectl autoscale deployment flask-deployment --cpu-percent=50 --min=2 --max=5
     ```
   - Verify the HPA configuration:
     ```bash
     kubectl get hpa
     ```
   - Simulate load to trigger autoscaling and observe the scaling behavior.

4. **Perform a Rolling Update:**
   - **Where to Run:** In your text editor and terminal.
   - Update the Deployment YAML (`flask-deployment.yaml`) to use a new image version (e.g., `flask-app:v2.0`).
   - Apply the updated Deployment:
     ```bash
     kubectl apply -f flask-deployment.yaml
     ```
   - Monitor the rollout status:
     ```bash
     kubectl rollout status deployment/flask-deployment
     ```
   - Verify that the new Pods are running the updated image.

5. **Rollback if Necessary:**
   - **Where to Run:** In your terminal or command prompt.
   - If the rolling update causes issues, rollback to the previous version:
     ```bash
     kubectl rollout undo deployment/flask-deployment
     ```
   - Verify the rollback:
     ```bash
     kubectl get pods
     ```

6. **Clean Up Resources:**
   - **Where to Run:** In your terminal or command prompt.
   - Delete the Service and Deployment:
     ```bash
     kubectl delete service flask-service
     kubectl delete deployment flask-deployment
     ```
   - Delete the HPA:
     ```bash
     kubectl delete hpa flask-deployment
     ```
   - Stop and delete the Minikube cluster (optional):
     ```bash
     minikube stop
     minikube delete
     ```

7. **Document Your Process:**
   - **Where to Run:** Use a text editor to create a Markdown (`.md`) or PDF report.
   - **What to Include:**
     - Step-by-step actions performed.
     - Kubernetes commands used and their outputs.
     - Configuration files (`Deployment`, `Service`, `HPA` YAMLs).
     - Screenshots of your terminal and browser (optional).
     - Any challenges encountered and how you resolved them.

### **Submission:**

- Submit your report in PDF or Markdown format via the course platform by **[Due Date]**.

---

## FAQs

**Q1: What is the difference between a Deployment and a ReplicaSet in Kubernetes?**

**A:** A **Deployment** manages the desired state of your application, including updates and scaling, by controlling ReplicaSets. A **ReplicaSet** ensures that a specified number of Pod replicas are running at any given time. While ReplicaSets are a lower-level concept, Deployments provide higher-level management features such as declarative updates and rollbacks.

---

**Q2: How does Horizontal Pod Autoscaler (HPA) determine when to scale?**

**A:** HPA monitors specified metrics (default is CPU utilization) and adjusts the number of Pod replicas to maintain the target metric value. For example, if the average CPU usage exceeds the target threshold, HPA will increase the number of replicas to distribute the load.

---

**Q3: Can I use custom metrics for HPA instead of CPU/memory?**

**A:** Yes, Kubernetes HPA supports custom metrics. You can configure HPA to scale based on application-specific metrics by integrating with monitoring tools like Prometheus and using the Kubernetes Metrics API.

---

**Q4: What happens during a rolling update in Kubernetes?**

**A:** During a rolling update, Kubernetes gradually replaces old Pods with new ones based on the updated Deployment configuration. It ensures that a minimum number of Pods are available at all times, minimizing downtime and maintaining application availability.

---

**Q5: How do I rollback a Deployment to a previous version?**

**A:** To rollback a Deployment, use the `kubectl rollout undo` command:
```bash
kubectl rollout undo deployment/flask-deployment
```
This command reverts the Deployment to the previous Revision, restoring the previous application version.

---