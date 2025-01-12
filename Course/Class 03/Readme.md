# Class 3: Kubernetes Networking and Storage for Python Applications

Welcome to **Class 3** of our Kubernetes course tailored for Python developers! In this session, you'll explore advanced networking concepts and storage solutions within Kubernetes. You'll learn how to configure networking for inter-service communication, implement network policies for security, and manage persistent storage using PersistentVolumes and PersistentVolumeClaims. By the end of this class, you'll be equipped to design and deploy robust, scalable, and secure Python applications on Kubernetes.

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

- **Understand** Kubernetes networking fundamentals and advanced concepts.
- **Configure** network policies to secure inter-service communication.
- **Implement** PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) for data persistence.
- **Integrate** storage solutions with Python applications in Kubernetes.
- **Troubleshoot** common networking and storage issues in Kubernetes deployments.

---

## Key Topics

### 1. Kubernetes Networking Fundamentals

- **Cluster Networking:**
  - Overview of Kubernetes networking model.
  - Pod-to-Pod communication.
  - Service discovery and DNS within the cluster.

- **Service Types:**
  - **ClusterIP:** Default service type, accessible only within the cluster.
  - **NodePort:** Exposes the service on each node’s IP at a static port.
  - **LoadBalancer:** Creates an external load balancer in supported cloud providers.
  - **ExternalName:** Maps a service to a DNS name.

### 2. Advanced Networking Concepts

- **Network Policies:**
  - Controlling traffic flow between Pods.
  - Defining ingress and egress rules.
  - Implementing security boundaries within the cluster.

- **Ingress Controllers:**
  - Managing external access to services.
  - Configuring Ingress resources for HTTP and HTTPS traffic.
  - Using popular Ingress controllers like NGINX and Traefik.

### 3. Kubernetes Storage Solutions

- **PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs):**
  - Understanding the PV and PVC lifecycle.
  - Dynamic provisioning of storage.
  - Binding PVs to PVCs.

- **Storage Classes:**
  - Defining storage types and policies.
  - Using different provisioners for various storage backends (e.g., AWS EBS, GCP Persistent Disks).

- **Volumes vs. PersistentVolumes:**
  - Differences and use-cases for various volume types (emptyDir, hostPath, etc.).

### 4. Integrating Storage with Python Applications

- **Mounting Volumes in Pods:**
  - Configuring volume mounts in Deployment manifests.
  - Managing read-write and read-only access.

- **Data Persistence Strategies:**
  - Ensuring data durability across Pod restarts and rescheduling.
  - Backup and restore procedures for PersistentVolumes.

### 5. Troubleshooting Networking and Storage

- **Common Networking Issues:**
  - DNS resolution failures.
  - Connectivity problems between Pods and Services.

- **Common Storage Issues:**
  - Volume binding errors.
  - Data access permissions.

- **Tools and Commands:**
  - Using `kubectl` for diagnosing issues.
  - Leveraging logs and metrics for troubleshooting.

---

## Step-by-Step Guide

### Step 1: Exploring Kubernetes Networking

#### **Step 1.1: Understand the Kubernetes Networking Model**

**Where to Run:** In your text editor and terminal.

1. **Cluster Networking Overview:**
   - Each Pod gets its own IP address.
   - Pods can communicate with each other without NAT.
   - Services provide stable IPs and DNS names for Pods.

2. **Service Discovery:**
   - Kubernetes DNS service resolves service names to ClusterIP addresses.
   - Example:
     ```bash
     kubectl get svc
     ```
     - Use service names within Pods to communicate (e.g., `http://flask-service:5000`).

#### **Step 1.2: Configure a Service with Different Types**

**Where to Run:** In your text editor and terminal.

1. **Create a ClusterIP Service (Default):**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: flask-service
   spec:
     selector:
       app: flask-app
     ports:
       - protocol: TCP
         port: 5000
         targetPort: 5000
     type: ClusterIP
   ```
   - **Apply the Service:**
     ```bash
     kubectl apply -f flask-service.yaml
     ```

2. **Create a NodePort Service:**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: flask-nodeport-service
   spec:
     selector:
       app: flask-app
     ports:
       - protocol: TCP
         port: 5000
         targetPort: 5000
         nodePort: 30001
     type: NodePort
   ```
   - **Apply the Service:**
     ```bash
     kubectl apply -f flask-nodeport-service.yaml
     ```

3. **Create a LoadBalancer Service (Cloud Providers Only):**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: flask-loadbalancer-service
   spec:
     selector:
       app: flask-app
     ports:
       - protocol: TCP
         port: 5000
         targetPort: 5000
     type: LoadBalancer
   ```
   - **Apply the Service:**
     ```bash
     kubectl apply -f flask-loadbalancer-service.yaml
     ```

### Step 2: Implementing Network Policies

#### **Step 2.1: Create a Namespace for Isolation**

**Where to Run:** In your terminal or command prompt.

1. **Create Namespace:**
   ```bash
   kubectl create namespace production
   ```

2. **Deploy Applications in the Namespace:**
   - Update Deployment and Service YAMLs to include `namespace: production` under `metadata`.

#### **Step 2.2: Define Network Policies**

**Where to Run:** In your text editor and terminal.

1. **Create a NetworkPolicy YAML (`deny-all.yaml`):**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: deny-all
     namespace: production
   spec:
     podSelector: {}
     policyTypes:
       - Ingress
       - Egress
   ```
   - **Apply the NetworkPolicy:**
     ```bash
     kubectl apply -f deny-all.yaml
     ```

2. **Allow Ingress from Specific Service (`allow-flask-ingress.yaml`):**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: allow-flask-ingress
     namespace: production
   spec:
     podSelector:
       matchLabels:
         app: flask-app
     policyTypes:
       - Ingress
     ingress:
       - from:
           - podSelector:
               matchLabels:
                 app: frontend-app
         ports:
           - protocol: TCP
             port: 5000
   ```
   - **Apply the NetworkPolicy:**
     ```bash
     kubectl apply -f allow-flask-ingress.yaml
     ```

### Step 3: Managing Persistent Storage

#### **Step 3.1: Create a PersistentVolume (PV)**

**Where to Run:** In your text editor and terminal.

1. **Create `persistent-volume.yaml`:**
   ```yaml
   apiVersion: v1
   kind: PersistentVolume
   metadata:
     name: flask-pv
   spec:
     capacity:
       storage: 1Gi
     accessModes:
       - ReadWriteOnce
     hostPath:
       path: "/mnt/data"
   ```
   - **Apply the PersistentVolume:**
     ```bash
     kubectl apply -f persistent-volume.yaml
     ```

#### **Step 3.2: Create a PersistentVolumeClaim (PVC)**

**Where to Run:** In your text editor and terminal.

1. **Create `persistent-volume-claim.yaml`:**
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: flask-pvc
     namespace: production
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 1Gi
   ```
   - **Apply the PersistentVolumeClaim:**
     ```bash
     kubectl apply -f persistent-volume-claim.yaml
     ```

#### **Step 3.3: Mount PVC in Deployment**

**Where to Run:** In your text editor and terminal.

1. **Update `flask-deployment.yaml` to Include Volume:**
   ```yaml
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
       volumeMounts:
       - mountPath: "/app/data"
         name: flask-storage
     volumes:
     - name: flask-storage
       persistentVolumeClaim:
         claimName: flask-pvc
   ```
   - **Apply the Updated Deployment:**
     ```bash
     kubectl apply -f flask-deployment.yaml
     ```

### Step 4: Troubleshooting Networking and Storage

#### **Step 4.1: Troubleshoot Pod Connectivity**

**Where to Run:** In your terminal or command prompt.

1. **Check Pod Status:**
   ```bash
   kubectl get pods -n production
   ```

2. **Describe Pod for Details:**
   ```bash
   kubectl describe pod <pod-name> -n production
   ```

3. **Test Connectivity Between Pods:**
   - **Execute Shell in a Pod:**
     ```bash
     kubectl exec -it <pod-name> -n production -- /bin/bash
     ```
   - **Ping Another Service:**
     ```bash
     ping flask-service
     ```

#### **Step 4.2: Troubleshoot Persistent Storage Issues**

**Where to Run:** In your terminal or command prompt.

1. **Check PVC Status:**
   ```bash
   kubectl get pvc -n production
   ```

2. **Describe PVC for Errors:**
   ```bash
   kubectl describe pvc flask-pvc -n production
   ```

3. **Check PV Binding:**
   ```bash
   kubectl get pv
   kubectl describe pv flask-pv
   ```

4. **Inspect Volume Mounts in Pods:**
   ```bash
   kubectl describe pod <pod-name> -n production
   ```

---

## Hands-On Demonstration

### **Configuring Networking and Persistent Storage for a Python Flask Application**

Follow these steps to enhance your Python Flask application with advanced networking policies and persistent storage in Kubernetes.

#### **Step 1: Create and Apply Network Policies**

**Where to Run:** In your text editor and terminal.

1. **Create `deny-all.yaml`:**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: deny-all
     namespace: production
   spec:
     podSelector: {}
     policyTypes:
       - Ingress
       - Egress
   ```
   - **Apply the NetworkPolicy:**
     ```bash
     kubectl apply -f deny-all.yaml
     ```

2. **Create `allow-flask-ingress.yaml`:**
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: allow-flask-ingress
     namespace: production
   spec:
     podSelector:
       matchLabels:
         app: flask-app
     policyTypes:
       - Ingress
     ingress:
       - from:
           - podSelector:
               matchLabels:
                 app: frontend-app
         ports:
           - protocol: TCP
             port: 5000
   ```
   - **Apply the NetworkPolicy:**
     ```bash
     kubectl apply -f allow-flask-ingress.yaml
     ```

#### **Step 2: Set Up Persistent Storage**

**Where to Run:** In your text editor and terminal.

1. **Create and Apply `persistent-volume.yaml`:**
   ```bash
   kubectl apply -f persistent-volume.yaml
   ```

2. **Create and Apply `persistent-volume-claim.yaml`:**
   ```bash
   kubectl apply -f persistent-volume-claim.yaml
   ```

3. **Update and Apply `flask-deployment.yaml`:**
   ```bash
   kubectl apply -f flask-deployment.yaml
   ```

4. **Create and Apply `flask-service.yaml`:**
   ```bash
   kubectl apply -f flask-service.yaml
   ```

#### **Step 3: Verify Networking and Storage**

**Where to Run:** In your terminal or command prompt and web browser.

1. **Check Network Policies:**
   ```bash
   kubectl get networkpolicies -n production
   ```

2. **Access the Flask Application:**
   - Retrieve Minikube IP:
     ```bash
     minikube ip
     ```
   - Navigate to `http://<minikube-ip>:30001` in your browser.

3. **Verify Data Persistence:**
   - Ensure that data written to `/app/data` persists across Pod restarts.

#### **Step 4: Troubleshoot Any Issues**

**Where to Run:** In your terminal or command prompt.

1. **Inspect Pods and Services:**
   ```bash
   kubectl get pods -n production
   kubectl get services -n production
   ```

2. **View Logs for Flask Application:**
   ```bash
   kubectl logs <flask-pod-name> -n production
   ```

3. **Check Volume Mounts:**
   ```bash
   kubectl describe pod <flask-pod-name> -n production
   ```

---

## Resources

- **Kubernetes Networking Documentation:** [https://kubernetes.io/docs/concepts/services-networking/](https://kubernetes.io/docs/concepts/services-networking/)
- **Network Policies in Kubernetes:** [https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- **Kubernetes Storage Documentation:** [https://kubernetes.io/docs/concepts/storage/](https://kubernetes.io/docs/concepts/storage/)
- **PersistentVolumes and PersistentVolumeClaims:** [https://kubernetes.io/docs/concepts/storage/persistent-volumes/](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
- **Kubernetes Secrets Documentation:** [https://kubernetes.io/docs/concepts/configuration/secret/](https://kubernetes.io/docs/concepts/configuration/secret/)
- **ConfigMaps in Kubernetes:** [https://kubernetes.io/docs/concepts/configuration/configmap/](https://kubernetes.io/docs/concepts/configuration/configmap/)
- **Minikube Networking:** [https://minikube.sigs.k8s.io/docs/handbook/networking/](https://minikube.sigs.k8s.io/docs/handbook/networking/)
- **Kubectl Cheat Sheet:** [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- **Python and Kubernetes Integration:** [https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)
- **Understanding Kubernetes Volumes:** [https://kubernetes.io/docs/concepts/storage/volumes/](https://kubernetes.io/docs/concepts/storage/volumes/)

---

## Assignment

### **Objective:**

Apply the concepts learned by configuring advanced networking policies and implementing persistent storage for a Python Flask application deployed on Kubernetes.

### **Tasks:**

1. **Configure Network Policies:**
   - **Where to Run:** In your text editor and terminal.
   - Create and apply a NetworkPolicy to deny all ingress and egress traffic by default.
   - Create and apply a NetworkPolicy to allow ingress traffic only from specific Pods or namespaces (e.g., a frontend application).

2. **Set Up Persistent Storage:**
   - **Where to Run:** In your text editor and terminal.
   - Create a PersistentVolume (PV) and PersistentVolumeClaim (PVC) for your Flask application.
   - Update your Deployment to mount the PVC to a specific path in the container.
   - Verify that data persists across Pod restarts.

3. **Deploy and Expose the Flask Application:**
   - **Where to Run:** In your text editor and terminal.
   - Apply the Deployment and Service YAML files.
   - Access the Flask application in your web browser to ensure it's running correctly.

4. **Manage Configurations with ConfigMaps and Secrets:**
   - **Where to Run:** In your text editor and terminal.
   - Create a ConfigMap for application configurations (e.g., environment variables).
   - Create a Secret for sensitive data (e.g., database credentials).
   - Update the Deployment to reference the ConfigMap and Secret.
   - Verify that the application correctly uses the configurations and secrets.

5. **Troubleshoot Networking and Storage Issues:**
   - **Where to Run:** In your terminal or command prompt.
   - Simulate common issues (e.g., incorrect NetworkPolicy rules, PVC binding failures) and resolve them using Kubernetes commands and logs.

6. **Document Your Setup:**
   - **Where to Run:** Use a text editor to create a Markdown (`.md`) or PDF report.
   - **What to Include:**
     - Step-by-step actions performed.
     - Kubernetes commands used and their outputs.
     - Configuration files (`NetworkPolicy`, `PersistentVolume`, `PersistentVolumeClaim`, `ConfigMap`, `Secret`, `Deployment`, `Service` YAMLs).
     - Screenshots of your terminal and browser (optional).
     - Any issues encountered and how you resolved them.

### **Submission:**

- Submit your report in PDF or Markdown format via the course platform by **[Due Date]**.

---

## FAQs

**Q1: What is the difference between a PersistentVolume and a PersistentVolumeClaim?**

**A:** A **PersistentVolume (PV)** is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using StorageClasses. A **PersistentVolumeClaim (PVC)** is a request for storage by a user that specifies size and access modes. PVCs consume PVs and allow Pods to use the storage.

---

**Q2: How do NetworkPolicies affect Pod communication?**

**A:** **NetworkPolicies** define rules that allow or deny traffic to Pods based on labels and namespaces. By default, if no NetworkPolicies are applied, all traffic is allowed. Once a NetworkPolicy is applied to a Pod, only the traffic that matches the policy rules is permitted, enhancing security by restricting unwanted communication.

---

**Q3: Can I use cloud provider storage solutions with Kubernetes?**

**A:** Yes, Kubernetes integrates with various cloud provider storage solutions such as AWS EBS, GCP Persistent Disks, and Azure Disks. You can define **StorageClasses** to provision these storage types dynamically, making it easier to manage storage based on your application's needs.

---

**Q4: What are the best practices for managing Secrets in Kubernetes?**

**A:** Best practices for managing Secrets in Kubernetes include:
- **Avoid storing sensitive data in plaintext:** Use Secrets to store sensitive information.
- **Enable encryption at rest:** Ensure Secrets are encrypted within etcd.
- **Use RBAC to control access:** Restrict access to Secrets using Role-Based Access Control.
- **Regularly rotate Secrets:** Update and rotate Secrets to minimize exposure risks.

---

**Q5: How can I monitor the health of my PersistentVolumes?**

**A:** You can monitor the health of your PersistentVolumes by:
- **Using `kubectl` commands:**
  ```bash
  kubectl get pv
  kubectl describe pv <pv-name>
  ```
- **Integrating monitoring tools:** Use Prometheus and Grafana to collect and visualize metrics related to storage usage and performance.
- **Setting up alerts:** Configure alerts for storage capacity thresholds or failures to ensure timely responses to issues.

---