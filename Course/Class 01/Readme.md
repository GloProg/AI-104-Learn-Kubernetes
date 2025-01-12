# Class 1: Introduction to Kubernetes

Welcome to **Class 1** of our Kubernetes course tailored for Python developers! In this session, you'll be introduced to the fundamental concepts of Kubernetes, its architecture, and how it orchestrates containerized applications. By the end of this class, you'll have a solid understanding of what Kubernetes is, its core components, and how to set up a basic Kubernetes environment.

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

- **Understand** the basic concepts and benefits of Kubernetes.
- **Identify** the key components of Kubernetes architecture.
- **Install** Kubernetes on your local machine using Minikube or Kubernetes-in-Docker (KinD).
- **Deploy** your first Kubernetes cluster and run a simple application.
- **Navigate** the Kubernetes ecosystem using `kubectl` commands.

---

## Key Topics

### 1. What is Kubernetes?

- **Definition and Purpose:**
  - Kubernetes is an open-source container orchestration platform that automates deploying, scaling, and managing containerized applications.

- **Benefits of Using Kubernetes:**
  - Automated deployment and scaling.
  - Self-healing capabilities.
  - Efficient resource utilization.
  - Declarative configuration and automation.

### 2. Kubernetes Architecture

- **Master Components:**
  - **API Server:** The front-end of the Kubernetes control plane.
  - **etcd:** A key-value store for cluster data.
  - **Scheduler:** Assigns workloads to nodes.
  - **Controller Manager:** Manages controllers for maintaining the desired state.

- **Node Components:**
  - **Kubelet:** Ensures containers are running in pods.
  - **Kube-Proxy:** Manages networking for pods.
  - **Container Runtime:** Software responsible for running containers (e.g., Docker, containerd).

### 3. Kubernetes Objects

- **Pods:** The smallest deployable units in Kubernetes, representing a single instance of a running process in a cluster.
- **Deployments:** Provide declarative updates for Pods and ReplicaSets.
- **Services:** Define logical sets of Pods and policies to access them.
- **ConfigMaps and Secrets:** Manage configuration data and sensitive information.

### 4. Setting Up Kubernetes

- **Local Installation Options:**
  - **Minikube:** Runs a single-node Kubernetes cluster locally.
  - **Kubernetes-in-Docker (KinD):** Runs Kubernetes clusters using Docker containers as nodes.

- **Installing `kubectl`:**
  - The command-line tool for interacting with Kubernetes clusters.

---

## Step-by-Step Guide

### Step 1: Installing Kubernetes Locally

#### **Step 1.1: Install `kubectl`**

**Where to Run:** In your terminal or command prompt.

1. **Download `kubectl`:**
   - Follow the [official Kubernetes installation guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/) for your operating system.

2. **Verify Installation:**
   ```bash
   kubectl version --client
   ```
   - **Expected Output:**
     ```
     Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.0", ...}
     ```

#### **Step 1.2: Install Minikube**

**Where to Run:** In your terminal or command prompt.

1. **Download Minikube:**
   - Follow the [official Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/) for your operating system.

2. **Start Minikube Cluster:**
   ```bash
   minikube start
   ```
   - **Explanation:**
     - Initializes a local Kubernetes cluster.

3. **Verify Cluster Status:**
   ```bash
   minikube status
   ```
   - **Expected Output:**
     ```
     host: Running
     kubelet: Running
     apiserver: Running
     kubeconfig: Configured
     ```

### Step 2: Deploying Your First Application

#### **Step 2.1: Create a Deployment**

**Where to Run:** In your terminal or command prompt.

1. **Deploy a Simple Nginx Application:**
   ```bash
   kubectl create deployment nginx --image=nginx
   ```
   - **Explanation:**
     - Creates a Deployment named `nginx` using the Nginx image.

2. **Verify Deployment:**
   ```bash
   kubectl get deployments
   ```
   - **Expected Output:**
     ```
     NAME    READY   UP-TO-DATE   AVAILABLE   AGE
     nginx   1/1     1            1           1m
     ```

#### **Step 2.2: Expose the Deployment as a Service**

**Where to Run:** In your terminal or command prompt.

1. **Expose Nginx Deployment:**
   ```bash
   kubectl expose deployment nginx --type=NodePort --port=80
   ```
   - **Explanation:**
     - Creates a Service to expose the Nginx Deployment on port 80 using a NodePort.

2. **Retrieve Service Details:**
   ```bash
   kubectl get services
   ```
   - **Expected Output:**
     ```
     NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
     nginx        NodePort    10.96.0.1       <none>        80:32456/TCP   2m
     kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        5m
     ```

#### **Step 2.3: Access the Application**

**Where to Access:** In your web browser.

1. **Retrieve Minikube IP:**
   ```bash
   minikube ip
   ```
   - **Example Output:**
     ```
     192.168.99.100
     ```

2. **Determine NodePort:**
   - From the `kubectl get services` output, identify the NodePort assigned to Nginx (e.g., `32456`).

3. **Access Nginx:**
   - Navigate to `http://192.168.99.100:32456` in your browser to see the Nginx welcome page.

### Step 3: Managing Kubernetes Objects

#### **Step 3.1: Scaling the Deployment**

**Where to Run:** In your terminal or command prompt.

1. **Scale Nginx Deployment to 3 Replicas:**
   ```bash
   kubectl scale deployment nginx --replicas=3
   ```
   - **Explanation:**
     - Increases the number of Pods in the Deployment to 3.

2. **Verify Scaling:**
   ```bash
   kubectl get deployments
   ```
   - **Expected Output:**
     ```
     NAME    READY   UP-TO-DATE   AVAILABLE   AGE
     nginx   3/3     3            3           3m
     ```

#### **Step 3.2: Updating the Deployment**

**Where to Run:** In your terminal or command prompt.

1. **Update Nginx Image to Latest:**
   ```bash
   kubectl set image deployment/nginx nginx=nginx:latest
   ```

2. **Monitor Rollout Status:**
   ```bash
   kubectl rollout status deployment/nginx
   ```
   - **Expected Output:**
     ```
     deployment "nginx" successfully rolled out
     ```

### Step 4: Cleaning Up Resources

#### **Step 4.1: Delete the Service and Deployment**

**Where to Run:** In your terminal or command prompt.

1. **Delete Service:**
   ```bash
   kubectl delete service nginx
   ```

2. **Delete Deployment:**
   ```bash
   kubectl delete deployment nginx
   ```

3. **Verify Deletion:**
   ```bash
   kubectl get deployments
   kubectl get services
   ```
   - **Expected Output:**
     - No entries for `nginx`.

#### **Step 4.2: Stop Minikube Cluster**

**Where to Run:** In your terminal or command prompt.

1. **Stop Minikube:**
   ```bash
   minikube stop
   ```

2. **Delete Minikube Cluster (Optional):**
   ```bash
   minikube delete
   ```

---

## Hands-On Demonstration

### **Deploying a Simple Nginx Application on Minikube**

Follow these steps to deploy and manage a simple Nginx application on your local Kubernetes cluster using Minikube.

#### **Step 1: Start Minikube Cluster**

**Where to Run:** In your terminal or command prompt.

```bash
minikube start
```

#### **Step 2: Create and Expose Nginx Deployment**

**Where to Run:** In your terminal or command prompt.

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
```

#### **Step 3: Access the Nginx Application**

**Where to Access:** In your web browser.

1. **Retrieve Minikube IP:**
   ```bash
   minikube ip
   ```
   - **Example Output:** `192.168.99.100`

2. **Retrieve NodePort:**
   ```bash
   kubectl get services
   ```
   - **Example Output:**
     ```
     NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
     nginx        NodePort    10.96.0.1       <none>        80:32456/TCP   2m
     kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        5m
     ```

3. **Navigate to Nginx:**
   - Open `http://192.168.99.100:32456` in your browser to view the Nginx welcome page.

#### **Step 4: Scale the Nginx Deployment**

**Where to Run:** In your terminal or command prompt.

```bash
kubectl scale deployment nginx --replicas=3
kubectl get deployments
```
- **Expected Output:**
  ```
  NAME    READY   UP-TO-DATE   AVAILABLE   AGE
  nginx   3/3     3            3           3m
  ```

#### **Step 5: Update the Nginx Deployment**

**Where to Run:** In your terminal or command prompt.

```bash
kubectl set image deployment/nginx nginx=nginx:latest
kubectl rollout status deployment/nginx
```
- **Expected Output:**
  ```
  deployment "nginx" successfully rolled out
  ```

#### **Step 6: Clean Up Resources**

**Where to Run:** In your terminal or command prompt.

```bash
kubectl delete service nginx
kubectl delete deployment nginx
minikube stop
minikube delete
```

---

## Resources

- **Kubernetes Official Documentation:** [https://kubernetes.io/docs/home/](https://kubernetes.io/docs/home/)
- **Minikube Documentation:** [https://minikube.sigs.k8s.io/docs/](https://minikube.sigs.k8s.io/docs/)
- **Kubectl Cheat Sheet:** [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- **Interactive Kubernetes Tutorial:** [https://kubernetes.io/docs/tutorials/kubernetes-basics/](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- **Python and Kubernetes Integration:** [https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/)

---

## Assignment

### **Objective:**

Apply the concepts learned by setting up a local Kubernetes environment and deploying a simple Python Flask application.

### **Tasks:**

1. **Install Kubernetes Tools:**
   - **Where to Run:** In your terminal or command prompt.
   - Install `kubectl` and Minikube following the steps outlined in the [Step-by-Step Guide](#step-by-step-guide).

2. **Start Minikube Cluster:**
   - **Where to Run:** In your terminal or command prompt.
   - Execute `minikube start` and verify the cluster status.

3. **Create a Deployment for Python Flask Application:**
   - **Where to Run:** In your terminal or command prompt.
   - Use `kubectl create deployment` to deploy a Flask application container.

4. **Expose the Deployment as a Service:**
   - **Where to Run:** In your terminal or command prompt.
   - Use `kubectl expose deployment` to create a Service with type `NodePort`.

5. **Access the Flask Application:**
   - **Where to Access:** In your web browser.
   - Retrieve the Minikube IP and NodePort, then navigate to the application URL to verify it's running.

6. **Scale the Deployment:**
   - **Where to Run:** In your terminal or command prompt.
   - Scale the Flask application to 3 replicas using `kubectl scale deployment`.

7. **Update the Deployment:**
   - **Where to Run:** In your terminal or command prompt.
   - Update the Flask application image to a newer version and monitor the rollout status.

8. **Clean Up Resources:**
   - **Where to Run:** In your terminal or command prompt.
   - Delete the Service and Deployment, then stop and delete the Minikube cluster.

9. **Document Your Setup:**
   - **Where to Run:** Use a text editor to create a Markdown (`.md`) or PDF report.
   - **What to Include:**
     - Step-by-step actions performed.
     - Kubernetes commands used and their outputs.
     - Screenshots of your terminal and browser (optional).
     - Any issues encountered and how you resolved them.

### **Submission:**

- Submit your report in PDF or Markdown format via the course platform by **[Due Date]**.

---

## FAQs

**Q1: What is the difference between a Deployment and a Service in Kubernetes?**

**A:** A **Deployment** manages the desired state and lifecycle of Pods, ensuring the specified number of replicas are running. A **Service** exposes a set of Pods as a network service, enabling stable access points and load balancing.

---

**Q2: How do I update my Kubernetes cluster to the latest version?**

**A:** For local clusters like Minikube, you can update by downloading the latest version and restarting the cluster:
```bash
minikube delete
minikube start --kubernetes-version=<latest-version>
```
For managed Kubernetes services (e.g., GKE, EKS, AKS), refer to the respective cloud provider's documentation for upgrade procedures.

---

**Q3: Can I run Kubernetes on Windows?**

**A:** Yes, Kubernetes can be run on Windows using tools like Minikube or Kubernetes-in-Docker (KinD). Ensure Docker Desktop is installed and configured correctly to support Kubernetes.

---

**Q4: How do I troubleshoot a failing Pod in Kubernetes?**

**A:** Use the following commands to troubleshoot:
- **Check Pod Status:**
  ```bash
  kubectl get pods
  ```
- **Describe the Pod:**
  ```bash
  kubectl describe pod <pod-name>
  ```
- **View Logs:**
  ```bash
  kubectl logs <pod-name>
  ```
- **Access Pod Shell:**
  ```bash
  kubectl exec -it <pod-name> -- /bin/bash
  ```

---

**Q5: What are Kubernetes Namespaces and how do I use them?**

**A:** **Namespaces** provide a way to divide cluster resources between multiple users or projects. They help in organizing and managing resources more effectively. To create a namespace:
```bash
kubectl create namespace <namespace-name>
```
To apply resources within a namespace:
```bash
kubectl apply -f <resource-file.yaml> -n <namespace-name>
```

---