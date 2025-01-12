# Class 2: Kubernetes Architecture and Components for Python Applications

Welcome to **Class 2** of our Kubernetes course tailored for Python developers! In this session, you'll delve deeper into the Kubernetes architecture, understand the roles of its core components, and explore how to configure and optimize your Kubernetes clusters for deploying Python applications. By the end of this class, you'll have a comprehensive understanding of Kubernetes' internal workings and how to leverage its features to manage your Python-based workloads effectively.

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

- **Understand** the detailed architecture of Kubernetes and its core components.
- **Identify** the roles and interactions between the Kubernetes master and worker nodes.
- **Configure** Kubernetes components for optimal performance and scalability.
- **Deploy** Python applications using Kubernetes Deployments and ReplicaSets.
- **Manage** application configurations and secrets securely within Kubernetes.

---

## Key Topics

### 1. Detailed Kubernetes Architecture

- **Master Components:**
  - **API Server:** Serves as the entry point for all administrative tasks.
  - **etcd:** A distributed key-value store for all cluster data.
  - **Scheduler:** Assigns workloads to worker nodes based on resource availability.
  - **Controller Manager:** Oversees various controllers that regulate the state of the cluster.
  
- **Worker Node Components:**
  - **Kubelet:** Ensures containers are running in Pods.
  - **Kube-Proxy:** Manages network communication both inside and outside the cluster.
  - **Container Runtime:** Executes containers (e.g., Docker, containerd).

### 2. Kubernetes Objects and Their Roles

- **Pods:** The smallest deployable units that can contain one or more containers.
- **ReplicaSets:** Ensure a specified number of pod replicas are running at any given time.
- **Deployments:** Provide declarative updates for Pods and ReplicaSets.
- **Services:** Define logical sets of Pods and policies to access them.
- **ConfigMaps and Secrets:** Manage configuration data and sensitive information separately from application code.

### 3. Configuring Kubernetes Components

- **Cluster Configuration:**
  - Setting up multiple master and worker nodes for high availability.
  - Configuring etcd for reliable cluster state storage.
  
- **Networking:**
  - Understanding the Container Network Interface (CNI).
  - Setting up network policies to control traffic flow.
  
- **Storage:**
  - Configuring PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs).
  - Integrating cloud storage solutions (e.g., AWS EBS, GCP Persistent Disks).

### 4. Deploying Python Applications

- **Creating Deployments:**
  - Defining Deployment YAML files for Python applications.
  - Rolling updates and rollback strategies.
  
- **Scaling Applications:**
  - Manually scaling deployments.
  - Autoscaling based on CPU/memory usage.

### 5. Managing Configurations and Secrets

- **Using ConfigMaps:**
  - Injecting configuration data into Pods.
  - Managing environment variables and application settings.
  
- **Using Secrets:**
  - Storing sensitive information securely.
  - Mounting secrets as environment variables or files within Pods.

---

## Step-by-Step Guide

### Step 1: Exploring Kubernetes Architecture

#### **Step 1.1: Understand Master and Worker Nodes**

**Where to Run:** In your text editor and terminal.

1. **Master Node Components:**
   - **API Server:** Acts as the frontend for the Kubernetes control plane.
   - **etcd:** Stores all cluster data.
   - **Scheduler:** Assigns Pods to worker nodes.
   - **Controller Manager:** Runs controller processes to regulate the cluster.

2. **Worker Node Components:**
   - **Kubelet:** Communicates with the API server to receive Pod specifications.
   - **Kube-Proxy:** Manages network rules on nodes.
   - **Container Runtime:** Runs containers as specified by Pods.

#### **Step 1.2: Visualize Kubernetes Components**

**Where to Run:** In your terminal or using online visualization tools.

1. **Use Kubernetes Dashboard:**
   ```bash
   minikube dashboard
   ```
   - **Explanation:**
     - Opens the Kubernetes Dashboard in your browser for a graphical view of cluster components and resources.

2. **Explore Cluster Components:**
   - Navigate through the Dashboard to view nodes, Pods, Deployments, Services, etc.

### Step 2: Configuring Kubernetes Components

#### **Step 2.1: Configure API Server**

**Where to Run:** In your terminal or configuration files.

1. **Access API Server Configuration:**
   - Typically managed by Kubernetes and not manually edited. However, understanding API server flags is essential for advanced configurations.

2. **Review API Server Logs:**
   ```bash
   kubectl logs -n kube-system <api-server-pod-name>
   ```

#### **Step 2.2: Set Up Networking with CNI**

**Where to Run:** In your terminal or using CNI plugins.

1. **Install a CNI Plugin (e.g., Calico):**
   ```bash
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```
   - **Explanation:**
     - Sets up Calico for network policy enforcement and Pod networking.

2. **Verify Network Plugin Installation:**
   ```bash
   kubectl get pods -n kube-system
   ```

### Step 3: Deploying Python Applications

#### **Step 3.1: Create a Deployment YAML for Flask App**

**Where to Run:** In your text editor.

1. **Create `flask-deployment.yaml`:**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: flask-app
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
   ```

2. **Apply the Deployment:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

#### **Step 3.2: Expose the Flask Application via a Service**

**Where to Run:** In your terminal or command prompt.

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

2. **Apply the Service:**
   ```bash
   kubectl apply -f flask-service.yaml
   ```

3. **Access the Flask Application:**
   - Retrieve Minikube IP:
     ```bash
     minikube ip
     ```
   - Navigate to `http://<minikube-ip>:30001` in your browser.

### Step 4: Managing Configurations and Secrets

#### **Step 4.1: Create a Secret for Database Credentials**

**Where to Run:** In your terminal or command prompt.

1. **Create a Secret YAML (`db-secret.yaml`):**
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: db-secret
   type: Opaque
   data:
     username: cG9zdGdyZXM=  # base64 encoded 'postgres'
     password: cGFzc3dvcmQ=  # base64 encoded 'password'
   ```

2. **Apply the Secret:**
   ```bash
   kubectl apply -f db-secret.yaml
   ```

#### **Step 4.2: Use ConfigMaps for Application Configuration**

**Where to Run:** In your text editor and terminal.

1. **Create a ConfigMap YAML (`app-config.yaml`):**
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: app-config
   data:
     APP_ENV: "production"
     LOG_LEVEL: "info"
   ```

2. **Apply the ConfigMap:**
   ```bash
   kubectl apply -f app-config.yaml
   ```

3. **Reference ConfigMap in Deployment:**
   - Update `flask-deployment.yaml` to include ConfigMap as environment variables:
     ```yaml
     envFrom:
     - configMapRef:
         name: app-config
     ```

4. **Apply the Updated Deployment:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

---

## Hands-On Demonstration

### **Deploying a Python Flask Application with ConfigMaps and Secrets**

Follow these steps to deploy a Python Flask application on Kubernetes, managing its configurations and sensitive data securely.

#### **Step 1: Create and Apply a Secret**

**Where to Run:** In your terminal or command prompt.

1. **Encode Credentials:**
   ```bash
   echo -n 'postgres' | base64
   echo -n 'password' | base64
   ```
   - **Example Output:**
     ```
     cG9zdGdyZXM=
     cGFzc3dvcmQ=
     ```

2. **Create `db-secret.yaml` with Encoded Data:**
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: db-secret
   type: Opaque
   data:
     username: cG9zdGdyZXM=
     password: cGFzc3dvcmQ=
   ```

3. **Apply the Secret:**
   ```bash
   kubectl apply -f db-secret.yaml
   ```

#### **Step 2: Create and Apply a ConfigMap**

**Where to Run:** In your text editor and terminal.

1. **Create `app-config.yaml`:**
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: app-config
   data:
     APP_ENV: "production"
     LOG_LEVEL: "info"
   ```

2. **Apply the ConfigMap:**
   ```bash
   kubectl apply -f app-config.yaml
   ```

#### **Step 3: Deploy the Flask Application**

**Where to Run:** In your text editor and terminal.

1. **Update `flask-deployment.yaml` to Include ConfigMap:**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: flask-app
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
   ```

2. **Apply the Deployment:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

#### **Step 4: Expose the Deployment via a Service**

**Where to Run:** In your text editor and terminal.

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

2. **Apply the Service:**
   ```bash
   kubectl apply -f flask-service.yaml
   ```

3. **Access the Flask Application:**
   - Retrieve Minikube IP:
     ```bash
     minikube ip
     ```
   - Navigate to `http://<minikube-ip>:30001` in your browser.

---

## Resources

- **Kubernetes Official Documentation:** [https://kubernetes.io/docs/home/](https://kubernetes.io/docs/home/)
- **Kubernetes Architecture Overview:** [https://kubernetes.io/docs/concepts/overview/components/](https://kubernetes.io/docs/concepts/overview/components/)
- **Kubectl Cheat Sheet:** [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- **Minikube Documentation:** [https://minikube.sigs.k8s.io/docs/](https://minikube.sigs.k8s.io/docs/)
- **Managing ConfigMaps and Secrets:** [https://kubernetes.io/docs/concepts/configuration/secret/](https://kubernetes.io/docs/concepts/configuration/secret/)
- **Python and Kubernetes Integration:** [https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)
- **Secure Kubernetes Deployment:** [https://kubernetes.io/docs/concepts/security/overview/](https://kubernetes.io/docs/concepts/security/overview/)
- **Prometheus Kubernetes Integration:** [https://prometheus.io/docs/prometheus/latest/getting_started/](https://prometheus.io/docs/prometheus/latest/getting_started/)
- **Grafana Kubernetes Monitoring:** [https://grafana.com/docs/grafana/latest/getting-started/getting-started-kubernetes/](https://grafana.com/docs/grafana/latest/getting-started/getting-started-kubernetes/)

---

## Assignment

### **Objective:**

Apply the concepts learned by understanding and configuring Kubernetes architecture, deploying a Python Flask application using Deployments and Services, and managing configurations and secrets securely.

### **Tasks:**

1. **Review Kubernetes Architecture:**
   - **Where to Run:** In your terminal or command prompt and text editor.
   - Study the master and worker node components.
   - Understand the interactions between the API Server, etcd, Scheduler, Controller Manager, Kubelet, Kube-Proxy, and Container Runtime.

2. **Set Up Kubernetes Cluster:**
   - **Where to Run:** In your terminal or command prompt.
   - Install `kubectl` and Minikube following the steps outlined in the [Step-by-Step Guide](#step-by-step-guide).
   - Start the Minikube cluster and verify its status.

3. **Deploy a Python Flask Application:**
   - **Where to Run:** In your text editor and terminal.
   - Create a Deployment YAML file (`flask-deployment.yaml`) for your Flask application.
   - Apply the Deployment using `kubectl apply -f flask-deployment.yaml`.

4. **Expose the Application via a Service:**
   - **Where to Run:** In your text editor and terminal.
   - Create a Service YAML file (`flask-service.yaml`) to expose the Flask application.
   - Apply the Service using `kubectl apply -f flask-service.yaml`.
   - Access the application in your web browser to verify it's running.

5. **Manage Configurations with ConfigMaps and Secrets:**
   - **Where to Run:** In your text editor and terminal.
   - Create a ConfigMap (`app-config.yaml`) for application settings.
   - Create a Secret (`db-secret.yaml`) for sensitive data like database credentials.
   - Update the Deployment to reference the ConfigMap and Secret.
   - Apply the updated Deployment and verify the configurations are correctly injected.

6. **Scale the Deployment:**
   - **Where to Run:** In your terminal or command prompt.
   - Scale the Flask application to 3 replicas using:
     ```bash
     kubectl scale deployment flask-app --replicas=3
     ```
   - Verify the scaling by listing the Pods:
     ```bash
     kubectl get pods
     ```

7. **Update the Deployment:**
   - **Where to Run:** In your text editor and terminal.
   - Modify the Flask application image or configuration.
   - Apply the changes using `kubectl apply -f flask-deployment.yaml`.
   - Monitor the rollout status:
     ```bash
     kubectl rollout status deployment/flask-app
     ```

8. **Clean Up Resources:**
   - **Where to Run:** In your terminal or command prompt.
   - Delete the Service and Deployment:
     ```bash
     kubectl delete service flask-service
     kubectl delete deployment flask-app
     ```
   - Stop and delete the Minikube cluster (optional):
     ```bash
     minikube stop
     minikube delete
     ```

9. **Document Your Process:**
   - **Where to Run:** Use a text editor to create a Markdown (`.md`) or PDF report.
   - **What to Include:**
     - Step-by-step actions performed.
     - Kubernetes commands used and their outputs.
     - Configuration files (`Deployment`, `Service`, `ConfigMap`, `Secret` YAMLs).
     - Screenshots of your terminal and browser (optional).
     - Any issues encountered and how you resolved them.

### **Submission:**

- Submit your report in PDF or Markdown format via the course platform by **[Due Date]**.

---

## FAQs

**Q1: What is the role of etcd in Kubernetes?**

**A:** etcd is a distributed key-value store that Kubernetes uses to store all cluster data, including configuration, state, and metadata. It ensures data consistency and reliability across the cluster.

---

**Q2: How do I access logs of a specific Pod?**

**A:** Use the `kubectl logs` command:
```bash
kubectl logs <pod-name>
```
- For multi-container Pods, specify the container name:
  ```bash
  kubectl logs <pod-name> -c <container-name>
  ```

---

**Q3: Can I run Kubernetes on Windows?**

**A:** Yes, Kubernetes can be run on Windows using tools like Minikube or Kubernetes-in-Docker (KinD). Ensure Docker Desktop is installed and configured correctly to support Kubernetes.

---

**Q4: How do ConfigMaps differ from Secrets in Kubernetes?**

**A:** ConfigMaps are used to store non-sensitive configuration data, while Secrets are intended for sensitive information like passwords, tokens, and keys. Secrets are stored securely and can be encrypted at rest.

---

**Q5: What should I do if my Deployment fails to rollout?**

**A:** 
1. **Check Deployment Status:**
   ```bash
   kubectl rollout status deployment/<deployment-name>
   ```
2. **Describe the Deployment:**
   ```bash
   kubectl describe deployment <deployment-name>
   ```
3. **Inspect Pod Logs:**
   ```bash
   kubectl logs <pod-name>
   ```
4. **Rollback to Previous Revision:**
   ```bash
   kubectl rollout undo deployment/<deployment-name>
   ```

---