# EKS Cluster Setup Guide

This guide will help you set up an EKS (Elastic Kubernetes Service) cluster on AWS and install ArgoCD for continuous delivery. The process includes creating necessary IAM roles, configuring the EKS cluster, setting up compute resources, and installing ArgoCD.

## 1. Create IAM Roles for EKS

### 1.1 Create Role for EKS Cluster
1. Log in to the [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to **IAM (Identity and Access Management)**.
3. Click on **Roles** and then click on **Create role**.
4. Choose **AWS Service** as the trusted entity.
5. Select **EKS-cluster** as the use case.
6. Click **Next** and provide a name for the role.

### 1.2 Create Role for EC2 Instances
1. In the AWS Management Console, navigate to **IAM**.
2. Click on **Roles** and then click on **Create role**.
3. Choose **AWS Service** as the trusted entity.
4. Select **EC2** as the use case.
5. Click **Next**.
6. Add the following policies:
   - `AmazonEC2ContainerRegistryReadOnly`
   - `AmazonEKS_CNI_Policy`
   - `AmazonEBSCSIDriverPolicy`
   - `AmazonEKSWorkerNodePolicy`
7. Provide a name for the role, e.g., `myNodeGroupPolicy`.

## 2. Create EKS Cluster

1. Navigate to **Amazon EKS** in the AWS Management Console.
2. Click on **Create cluster**.
3. Enter the desired cluster name, select the Kubernetes version, and specify the IAM role created in step 1.1.
4. Configure Security Groups, Cluster Endpoint, and other settings as needed.
5. Click **Next** and proceed to create the cluster.

## 3. Create Compute Resources

1. In the Amazon EKS console, navigate to **Compute** or **Node groups**.
2. Click **Add Node Group** and provide a name for the compute resource.
3. Select the IAM role created in step 1.2.
4. Choose the Node Type and Size.
5. Click **Next** and proceed to create the compute resource.

## 4. Configure AWS Cloud Shell

1. Open **AWS Cloud Shell** or use the **AWS CLI**.
2. Execute the following command to update the kubeconfig for your EKS cluster:
   ```bash
   aws eks update-kubeconfig --name <cluster-name> --region <region>

These steps should help you in setting up your EKS cluster along with necessary roles and compute resources.


# Install ArgoCD

Here are the steps to install ArgoCD and retrieve the admin password:

1. **Create Namespace for ArgoCD**:
   ```bash
   kubectl create namespace argocd
   ```

2. **Apply ArgoCD Manifests**:
   ```bash
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
   ```

3. **Patch Service Type to LoadBalancer**:
   ```bash
   kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
   ```

4. **Retrieve Admin Password**:
   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```

These commands will install ArgoCD into the specified namespace, set up the service as a LoadBalancer, and retrieve the admin password for you to access the ArgoCD UI.
