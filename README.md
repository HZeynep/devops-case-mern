# DevSecOps with Kubernetes & MERN Stack

This repository contains my implementation of a **DevSecOps pipeline** applied to a **MERN Stack** web application. The project leverages **AWS**, **Kubernetes (EKS)**, **Terraform**, **Prometheus**, **Grafana**, **ArgoCD**, and **GitHub Actions** for a complete automation pipeline, secure deployment, and real-time monitoring of a scalable application.

---

## Project Overview

In this project, I automated the deployment of a **MERN stack** (MongoDB, Express, React, Node.js) application using **DevSecOps** principles, leveraging **AWS** for infrastructure provisioning, **Kubernetes (EKS)** for container orchestration, **Terraform** for infrastructure as code, and **ArgoCD** for GitOps practices. Real-time application performance is monitored using **Prometheus** and **Grafana**.

### Key Technologies

- **MERN Stack**: MongoDB, Express, React, Node.js
- **AWS**: EC2, S3, ECR, and EKS.
- **Terraform**: Infrastructure as Code (IaC) for provisioning AWS resources.
- **Kubernetes (EKS)**: Orchestrates the containerized applications.
- **ArgoCD**: GitOps tool for continuous delivery and deployment.
- **Prometheus & Grafana**: Monitoring the performance and health of the Kubernetes cluster.
- **GitHub Actions**: Automates the CI/CD pipeline.

---

## Setup Instructions

### 1. Clone the Repository

Clone the repository to your local machine:

```bash
git clone https://github.com/HZeynep/devops-case-mern.git
cd devops-case-mern


## 2. Provision AWS Resources with Terraform

This step involves setting up AWS resources using **Terraform** to automate the infrastructure provisioning. We’ll create an **IAM user**, configure an **EKS cluster**, **VPC**, **Load Balancer**, and other necessary resources.

### 2.1. Creating IAM User and Access
1. Create an IAM user in **AWS Management Console** with programmatic access.
2. Assign necessary permissions for creating resources like EC2, EKS, VPC, etc.
3. Generate access keys for the IAM user to use with **Terraform** for infrastructure automation.

### 2.2. Setting Up AWS Resources with Terraform
1. **Configure Terraform**: First, set up **Terraform** with the necessary provider configurations for AWS.
   - Define the AWS provider in the Terraform configuration file.
   - Set AWS credentials either using environment variables or `~/.aws/credentials` file.
   
2. **Define Resources**: Using Terraform, define resources such as:
   - **VPC**: A virtual private cloud to deploy the EKS cluster and other resources.
   - **EKS Cluster**: A fully managed Kubernetes service.
   - **Security Groups**: To define rules for allowing traffic to and from the EKS cluster.
   - **ALB**: Configure an **Application Load Balancer** to expose your application.

3. **Run Terraform Commands**:
   - `terraform init`: Initialize the Terraform configuration.
   - `terraform plan`: Plan the changes to be applied to AWS.
   - `terraform apply`: Apply the configuration to create the resources.

4. **Verify the Resources**: Once resources are provisioned, use the AWS console or Terraform to confirm that all components have been created successfully.

---

## 3. Deploy Jenkins on AWS

To facilitate the CI/CD pipeline, we will deploy **Jenkins** on **AWS EC2**.

### 3.1. Deploy Jenkins on EC2
1. Provision an **EC2 instance** using Terraform with a publicly accessible IP.
2. Install **Jenkins** on the instance. This can be done by running the following commands:
   ```bash
   sudo apt update
   sudo apt install openjdk-11-jdk
   wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
   sudo sh -c 'echo deb http://pkg.jenkins.io/debian/ / > /etc/apt/sources.list.d/jenkins.list'
   sudo apt update
   sudo apt install jenkins
   sudo systemctl start jenkins
   sudo systemctl enable jenkins


   ### 3.2. Access Jenkins

To access Jenkins, open port **8080** on the **Security Group** associated with your EC2 instance to allow web traffic.

1. **Update Security Group**:
   - In the AWS **EC2 Dashboard**, navigate to **Security Groups**.
   - Select the security group attached to your Jenkins EC2 instance.
   - Add a new **Inbound Rule** for port **8080** with **Source** set to **0.0.0.0/0** (for public access) or to a specific IP range as per your security requirements.

2. **Access Jenkins**:
   - Open a browser and navigate to `http://<EC2-Public-IP>:8080`, where `<EC2-Public-IP>` is the public IP address of your EC2 instance.
   - You should be presented with the Jenkins initial setup screen.

### 3.3. Complete the Initial Jenkins Setup

1. **Unlock Jenkins**:
   - Jenkins will display an unlock screen. To retrieve the **Jenkins unlock key**, run the following command on your EC2 instance:
     ```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
     ```
   - Copy the password from the command output.

2. **Set Up Jenkins**:
   - Paste the unlock key into the Jenkins setup screen to proceed.
   - Follow the on-screen instructions to complete the Jenkins setup:
     - **Install Suggested Plugins**: Choose the option to install the suggested plugins, which include essential plugins for building and deploying projects.
     - **Create Admin User**: After the plugins are installed, you will be prompted to create an **Admin User** with a username, password, and email.
     - **Jenkins URL**: Ensure the Jenkins URL is correct and adjust it if necessary.

3. **Finish Setup**:
   - Once the setup is complete, you can start using Jenkins for building and automating your projects.
   - You can now access the Jenkins dashboard at `http://<EC2-Public-IP>:8080`.

---

## 4. Configure Prometheus and Grafana for Monitoring

Once Jenkins is set up, you can proceed with configuring **Prometheus** and **Grafana** for monitoring purposes.

### 4.1. Install Prometheus on Kubernetes Cluster
1. Follow the steps to install **Prometheus** and set up a monitoring stack in the Kubernetes cluster.
   - Use **Helm** for installation and configure Prometheus to scrape Kubernetes metrics.
   
### 4.2. Install Grafana on Kubernetes Cluster
1. Similarly, install **Grafana** using Helm.
2. Set up **Grafana** as the dashboard to visualize metrics collected by **Prometheus**.
3. Use the **Prometheus** data source in Grafana to display Kubernetes cluster performance metrics.

### 4.3. Configure ArgoCD for GitOps

ArgoCD is used to implement GitOps for continuous delivery and deployment. Follow the steps to set up ArgoCD for automating deployments of your Kubernetes applications.

#### 4.3.1. Install ArgoCD
1. Install **ArgoCD** on your Kubernetes cluster using Helm:
   ```bash
   helm install argo/argo-cd --namespace argocd --create-namespace

### 4.3.3. Expose the ArgoCD API Server

To access the ArgoCD dashboard, you'll need to expose the ArgoCD API server by port-forwarding:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443

### 4.3.4. Access the ArgoCD Web UI

1. Open your browser and go to `http://localhost:8080`.
2. Log in to the ArgoCD dashboard with the default username (`admin`) and password. To retrieve the initial password, run the following command:

   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 --decode
### 4.4. Add GitHub Repository to ArgoCD

1. In the **ArgoCD** dashboard, navigate to **Settings** and select **Repositories**.
2. Click **CONNECT REPO USING HTTPS** to add a new GitHub repository.
3. Provide the repository name (where your Kubernetes manifests are stored).
4. For **Username**, provide your **GitHub username** and for **Password**, use your **GitHub Personal Access Token**.
5. Click **CONNECT** to link your GitHub repository to ArgoCD.
6. If the connection is successful, you will see a green notification confirming that the repository is linked.

---

### 4.5. Create Applications in ArgoCD

Now that your repository is connected, you can begin deploying your applications using ArgoCD.

#### 4.5.1. Database Application

1. In the **ArgoCD dashboard**, click on **CREATE APPLICATION**.
2. Provide the application details, such as the name, project, and namespace.
3. In the **Source** section, select the repository you just connected and specify the path where the Kubernetes manifests for the database are located.
4. Select the **destination** cluster and namespace, then click **CREATE** to deploy the database application.

#### 4.5.2. Backend Application

1. Repeat the steps for creating a new application, but this time for the backend component of your Three-Tier application.
2. Specify the appropriate repository path for the backend manifests.
3. Click **CREATE** to deploy the backend application.

#### 4.5.3. Frontend Application

1. Similarly, create an application for the frontend of your Three-Tier application.
2. Select the relevant repository path for the frontend manifests.
3. Click **CREATE** to deploy the frontend application.

#### 4.5.4. Ingress Application

1. Lastly, create an **Ingress** application to expose the Three-Tier application to the outside world.
2. Provide the necessary repository path for the Ingress manifests.
3. Click **CREATE** to deploy the ingress.

---

### 4.6. Expose the Application with a Load Balancer

Once all your applications are deployed, an **Application Load Balancer (ALB)** will be created to expose the frontend of your Three-Tier application.

1. After the ingress is deployed, check for the **ALB DNS** in the **AWS EC2** console or the ArgoCD dashboard.
2. Copy the **ALB DNS name** and set up a **CNAME record** in your DNS provider, pointing it to the ALB.
   - Example: If your domain is managed by **Porkbun**, add a **CNAME record** like `backend.yourdomain.com` pointing to the **ALB DNS**.

---

### 4.7. Validate Application Deployment

Once your DNS changes have propagated (this may take a few minutes), you can visit the newly created subdomain (e.g., `backend.yourdomain.com`) in your browser. You should see your **Three-Tier Application** running successfully.

---

### 5. Monitor Kubernetes Cluster with Prometheus and Grafana

#### 5.1. Access Grafana

1. Open your browser and navigate to the **Grafana Web UI** using the **ALB DNS**.
2. Log in using the default credentials:
   - **Username**: `admin`
   - **Password**: `prom-operator`

#### 5.2. Create Dashboards

1. Once logged in, click **Dashboard** to create a new monitoring dashboard.
2. You can either import a pre-built Kubernetes dashboard or create a custom one to monitor your cluster.
3. Set the data source as **Prometheus**.

#### 5.3. Visualize Kubernetes Metrics

Grafana can be used to monitor important metrics such as:
- **Pod health**
- **Resource usage (CPU, memory, etc.)**
- **Deployment status**

---

### Conclusion

In this guide, we've successfully:
- Provisioned AWS resources using **Terraform**.
- Set up **Jenkins** for continuous integration and delivery.
- Deployed a Kubernetes cluster using **Amazon EKS**.
- Configured **Prometheus** and **Grafana** for monitoring.
- Implemented **GitOps** with **ArgoCD** to automate application deployments.
- Deployed a **Three-Tier Application** with persistent storage using **Persistent Volumes**.
- Set up a **Load Balancer** to expose the application.
- Monitored the Kubernetes cluster and application health using **Grafana** and **Prometheus**.

This end-to-end DevSecOps project demonstrates how modern CI/CD, GitOps, and monitoring practices can be applied to deploy and manage applications on Kubernetes with robust monitoring and security practices. Feel free to extend the project further with advanced scaling, backup strategies, and enhanced security features.




