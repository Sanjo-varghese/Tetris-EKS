# Tetris-EKS
# GitOps: Deploying Tetris Game on Amazon EKS Using ArgoCD

![terties](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/7b122a4d-71a0-450a-8b55-23205cb5117b)

# STEP 1: Create IAM Roles
**Let's First start creating two IAM roles one is for Cluster and another is for Nodegroup**
**Go to Aws console and search for IAM**
**You will be redirected to the IAM dashboard**
**Click "Roles"**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/3029fb51-ced1-4a03-b4b4-05b080837b32)

**Click "Create role"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/ad3486c9-128c-4b8d-914a-4b4996fe2a00)

**Click "Allow AWS services like EC2, Lambda, or others to perform actions in this account.**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/09092933-1aec-4a8c-bf93-6b3a969983a5)

**Click "Choose a service or use case"**
 
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/32115e5e-3791-4890-98a1-f53682142de4)
**Click "EC2"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/256466fa-268e-41fb-ac95-945d13bbab1f)

**Click "Next"**
**Click the "Search" field.**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/7796d8ba-a638-4da2-9835-408fb93a7e29)

# Search these Policy Names and make it check I already have these in
it )
- AmazonEC2ContainerRegistryReadOnly
- AmazonEKS_CNI_Policy
- AmazonEBSCSIDriverPolicy
- AmazonEKSWorkerNodePolicy
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/e0c3b657-b7b2-4d12-9edb-e18735a596b8)
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/f03b6539-a766-483c-bd9d-1468dedbbd2f)
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/89b8b4ba-24de-4467-9205-5d13dacb23f2)
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/bfb6f58b-de63-4f9e-854b-6be315d696ef)

**Click "Next"** --->**Click the "Role name" field.**--->**Add Role name as myAmazonNodeGroupPolicy**
**Click "Create role"**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/27a56c38-125b-4f8b-8f92-a044d69c61cc)

**NodeGroup Role is created.**

 # Step 2: Create EKS Cluster
**Click the "Search" field and search For EKS or select directly Elastic**
**Kubernetes Service on the Recently visited tab**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/60e91c70-800b-4e10-b447-0ceead39f578)

**Click "Add cluster"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/b0d43e69-f2fe-4f35-b525-d5bd963058b6)

**Click "Create"**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/50be8fe5-dca7-4f5a-8cd4-2a36c73b4ecb)


**Click the "Name" field and enter a unique name for the cluster that is anything you want. For example, I used Cloud and version 1.28 Click "myAmazonEKSClusterRole" which is created in step 1.**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/2b08ec09-28c8-47ad-a8e2-6a54ff1b6b24)

**Click "Next"**
**Click "Select security groups" and Use the existing security group or create a new security Group**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/fafd5022-457f-4f0e-94e6-91b74aaa1b8e)

**Click "Next"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/f3071aa6-10e3-4543-b2f0-911e38976137)
**Click "Next"**

**No changes Click "Next" (Default no need to change anything)
No changes Click "Next" (Default no need to change anything)
Click "Create"
It takes 15 minutes to create.**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/aa25de4f-ad24-4d00-8ac6-131971be54b6)

**Once your Cluster up to active status**
**Click "Compute"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/007289cf-020d-46c2-bc31-16d662ecf3ff)
**Click on "Add node group"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/6adae03a-e425-47a7-9deb-b2cd052f9c94)
**Click the "Name" field.**
**Write any Name you want ( NodeGroup )**
**Select the Role that was created for the node Group in Step 1**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/c93ca15e-7a80-4653-93cf-31cdef41c52a)
**Click "Next"**
**On the next page remove t3.medium and add t2.medium as instance type.**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/ec010b5e-b5f5-4e5c-b567-1be6535f3659)
**Select t2.medium**
