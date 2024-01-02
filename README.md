# Tetris- Game
# GitOps: Deploying Tetris Game on Amazon EKS Using ArgoCD

![images](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/81f8941e-a165-4971-a91e-3f0404bb70cb)


# What Is Argo CD?
**Argo CD is a Kubernetes-native continuous deployment (CD) tool. Unlike external CD tools that only enable push-based deployments, Argo CD can pull updated code from Git repositories and deploy it directly to Kubernetes resources. It enables developers to manage both infrastructure configuration and application updates in one system.**

**Argo CD offers the following key features and capabilities:**

- Manual or automatic deployment of applications to a Kubernetes cluster.
- Automatic synchronization of application state to the current version of declarative configuration.
- Web user interface and command-line interface (CLI).
- Ability to visualize deployment issues, detect and remediate configuration drift.
- Role-based access control (RBAC) enabling multi-cluster management.
- Single sign-on (SSO) with providers such as GitLab, GitHub, Microsoft, OAuth2, OIDC, LinkedIn, LDAP, and SAML 2.0
- Support for webhooks triggering actions in GitLab, GitHub, and BitBucket.
- This is part of an extensive series of guides about Kubernetes.

# GitOps with Argo CD
- GitOps is a software engineering practice that uses a Git repository as its single source of truth. Teams commit declarative configurations into Git, and these configurations are used to create environments needed for the continuous delivery process. There is no manual setup of environments and no use of standalone scripts—everything is defined through the Git repository.

- A basic part of the GitOps process is a pull request. New versions of a configuration are introduced via pull request, merged with the main branch in the Git repository, and then the new version is automatically deployed. The Git repository contains a full record of all changes, including all details of the environment at every stage of the process.

- Argo CD handles the latter stages of the GitOps process, ensuring that new configurations are correctly deployed to a Kubernetes cluster. 

- At a high level, the Argo CD process works like this:

- A developer makes changes to an application, pushing a new version of Kubernetes resource definitions to a Git repo.
Continuous integration is triggered, resulting in a new container image saved to a registry. 
- A developer issues a pull request, changing Kubernetes manifests, which are created either manually or automatically.
- The pull request is reviewed and changes are merged to the main branch. This triggers a webhook which tells Argo CD a change was made.
- Argo CD clones the repo and compares the application state with the current state of the Kubernetes cluster. It applies the required changes to cluster configuration.
- Kubernetes uses its controllers to reconcile the changes required to cluster resources, until it achieves the desired configuration.
- Argo CD monitors progress and when the Kubernetes cluster is ready, reports that the application is in sync.
- ArgoCD also works in the other direction, monitoring changes in the Kubernetes cluster and discarding them if they don’t match the - 
- current configuration in Git.

# Infrastructure
![terties](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/7b122a4d-71a0-450a-8b55-23205cb5117b)

 # prerequisites

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
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/0a8f5026-f2aa-474c-9afc-b69f9f65ee98)
**Click "Next"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/030c55b1-181f-4ef1-a3b1-375efd7ebc75)
**Click "Next"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/48b3b200-3475-4fbc-9488-33324ea865f5)
**Click "Create"**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/da44fdb7-a460-446e-b5a5-6560f3d7828e)
**Node Groups will take some time to create.**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/492f17f5-6e6e-4960-8da3-8cd6a86d227b)
**Worker nodes created.**

# Step 3: ARGOCD SETUP
**Click on the AWS cloud shell icon on the top right**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/cc0fcc83-0ee3-4185-bbe6-1e5b643c74af)

**click on connect**
**Better to open in a new tab**
**First set context by providing the following command**

```sh
aws eks update-kubeconfig --name EKS_CLUSTER_NAME --region CLUSTER_
```
**#example**
- aws eks update-kubeconfig --name GitOps --region ap-south-1

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/0a4bc464-7147-445b-af44-6f2412dec982)
**Check for Nodes**

```sh
kubectl get nodes
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/067fb465-e21e-49d7-80cc-da3c1ddbe923)

**Check for pods, You will get no resources found.**
```sh
kubectl get pods
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/24e79f73-6865-48d5-9c36-b421e43208d0)

 # Let's install ArgoCD
 ArgoCD Installation URL: https://archive.eksworkshop.com/intermediate/290_argocd/install/

 **You will redirected to this page**
 ![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/252fba19-5ec8-4bf9-b6c9-f2c1bee26aaf)
**All those components could be installed using a manifest provided by the Argo Project: use the below commands**:

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/e21414d6-e66e-4631-bceb-249eb261eb01)

**COMMANDS ARGOCD**
- By default, argocd-server is not publicly exposed. For this project, we will use a Load Balancer to make it usable:

```sh
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/0d3f6cc2-4bd8-4941-b991-42e8548919da)

**One load balancer will created in the AWS**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/7cd44d50-137f-4ec4-b5d2-2494c3c4ee10)
**Wait about 2 minutes for the LoadBalancer creation**

```sh
export ARGOCD_SERVER=`kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname'`
```
**when you run this command, it will export the hostname of the ArgoCD server's load balancer and store it in the ARGOCD_SERVER environment variable, which you can then use in other commands or scripts to interact with the ArgoCD server. This can be useful when you need to access the ArgoCD web UI or interact with the server programmatically.**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/25d12c3a-0270-4f85-b54f-c673b70c147d)

**If run this command you will get the load balancer external IP**

```sh
echo $ARGOCD_SERVER
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/2efedd84-7915-42b5-9202-6cbafa6b0b05)

# Login
**The command you provided is used to extract the password for the initial admin user of ArgoCD, decode it from base64 encoding, and store it in an environment variable named** *ARGO_PWD.*

```sh
export ARGO_PWD=`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/7760bedf-434f-49f7-9846-1335cfc7fccb)

**If you want to see your password provide the below command**

```sh
echo $ARGO_PWD
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/21f0871e-e6ff-45b7-9e93-400aa859263e)
**Now copy the load balancer IP and paste it into the browser**
```sh
echo $ARGOCD_SERVER
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/80de0eea-da80-4d7f-8d52-cd17b9b5e12c)
**Now you will see this page. if you get an error click on advanced and click on proceed.**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/76e7b0b4-440e-4b1a-8525-60d865b13b16)

**Now you will see this page and log in to ArgoCD**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/8f4ab737-7f08-4d6c-91aa-ecb7ca46e01c)

**Username is "admin"**
**For the password, you have to provide the below command and copy it**
```sh
echo $ARGO_PWD
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/14127ec1-4b06-4990-87e9-f65be02121aa)
**Click on Signin and you will see this page.**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/d012079f-abe5-410f-8e7d-cf0dc6dd8d5e)

**Now click on the Setting gear icon in the left side panel**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/205a5cc3-9c27-48df-b0ef-e8fefd9af595)
**Click on Repositories**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/2c2572a4-b330-4a7d-a219-cde35b982a89)

**Now click on Connect Repo Using HTTPS**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/92d7d87f-57d4-474a-b95d-4dac31ec673c)


**Add Github details, Type as git, Project as default and provide the
GitHub URL of this project and click on connect**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/426d9bee-6378-4970-b512-bd25fbec546d)

**You will get Connection Status as Successful**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/706aefe3-a35a-42f2-9e25-0ec4b42eba53)

**Click on Manage Your application**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/86d7f9af-8e33-4222-bd8c-9e3d8ff68e35)

**You will see this page and click on New App**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/e86c6b0c-b1fd-4c87-9192-533612ff1c3f)
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/5d36bbe4-6698-4402-8885-a958cca1d12d)
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/d135790c-b841-44d8-82fc-9a5288f51f94)

**Finally, click on Create**
**In the deployment file, we used the image as**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/8b6a80bf-160c-44af-9297-5a3ecdcbc863)

**Now you will see a new App has been created named tetris**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/eae257b4-c547-43b9-bed7-4b88d43be403)

**Click on tetris**
**Now click on three dots beside tetris-service and click on the details**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/d261499e-8498-4bbb-8540-17a7dbbc1813)

**Now copy the hostname address**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/63894f8a-0714-4be5-b415-6574aa39e2e3)

**Paste it in a browser you will see this page**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/977c629b-bba7-4fc0-9bae-8476ce8e4d7b)
**Now play the game of version 1.**

# Step 4: Change the version of the Game
**Now the change version of the game in the deployment file**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/14980a12-55a1-4f2d-b71e-324e6734cc55)

**ArgoCD now automatically starts the build when you change anything in your Repository or the files**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/943b35b6-5151-4d95-8acb-966d7edcda22)

**Now Last Sync id changed**
**Click on three dots at service and copy the hostname address**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/b23f30a7-0859-4563-b0f5-36237aed37a1)
**Now you will see the second version of the Game**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/3fdf0f6f-d684-4218-b9aa-634f659bdc3a)

**enjoy** .. **Let's Break the Highest score in Tetris.**

```sh
kubectl get all
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/3910db25-0e69-4682-91ce-df0319d8e68f)

# Step 5: Termination
**Go to Cloud shell and delete service for ArgoCD load balancer**

```sh
kubectl delete svc argocd-server -n argocd
```
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/ead26ef0-ea8f-49c6-879e-01ee8cbb40f3)

**Go to AWS and manually delete the load balancer if it is not deleted.**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/776ed13b-4202-438d-86d1-1ad01ae6e7ae)

**Delete Nodegroup First**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/3c0f69f9-4a1a-4872-a6e2-a8c4e382c5c7)

**Provide the name that you used for your node group and click delete**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/262a10c5-7622-4b62-acea-929d99a543b6)
**Delete the Cluster**
![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/148555cd-98f5-4c53-b560-8bf88f38ae99)
**Provide the name of the Cluster and delete.**

![image](https://github.com/Sanjo-varghese/Tetris-EKS/assets/116708794/7bc464f9-9091-4db0-9992-d4230f3f72cd)
**As we conclude our journey into the fascinating realm of GitOps and the thrilling world of Tetris on Kubernetes, let's leave you with a fun fact:
Did you know that the creator of Tetris, Alexey Pajitnov, designed the game in 1984 while working as a computer scientist at the Soviet Academy of Sciences? What started as a simple experiment turned into a worldwide gaming sensation that has captured the hearts and minds of players for decades.**


**©Cloud champ**
**Thanks- Nasiullha for your Project**
<h1 align="center">Hi 👋, I'm SANJO VARGHESE</h1>
<h3 align="center">IT Professional</h3>

<p align="left"> <img src="https://komarev.com/ghpvc/?username=sanjo-varghese&label=Profile%20views&color=0e75b6&style=flat" alt="sanjo-varghese" /> </p>

- 💬 Ask me about **System and cloud related technologies**

- 📫 How to reach me **sanjovarghese484@gmail.com**

<h3 align="left">Connect with me:</h3>
<p align="left">
<a href="https://linkedin.com/in/sanjo-varghese" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="sanjo-varghese" height="30" width="40" /></a>
<a href="https://fb.com/sanjovarghese.net" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/facebook.svg" alt="sanjovarghese.net" height="30" width="40" /></a>
<a href="https://www.instagram.com/sanjo._.varghese" target="blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/instagram.svg" alt="www.instagram.com/sanjo._.varghese" height="30" width="40" /></a>
</p>

<h3 align="left">Languages and Tools:</h3>
<p align="left"> <a href="https://aws.amazon.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/amazonwebservices/amazonwebservices-original-wordmark.svg" alt="aws" width="40" height="40"/> </a> <a href="https://azure.microsoft.com/en-in/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/microsoft_azure/microsoft_azure-icon.svg" alt="azure" width="40" height="40"/> </a> <a href="https://www.w3schools.com/cpp/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/cplusplus/cplusplus-original.svg" alt="cplusplus" width="40" height="40"/> </a> <a href="https://www.w3schools.com/css/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/css3/css3-original-wordmark.svg" alt="css3" width="40" height="40"/> </a> <a href="https://www.docker.com/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/docker/docker-original-wordmark.svg" alt="docker" width="40" height="40"/> </a> <a href="https://cloud.google.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/google_cloud/google_cloud-icon.svg" alt="gcp" width="40" height="40"/> </a> <a href="https://git-scm.com/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/git-scm/git-scm-icon.svg" alt="git" width="40" height="40"/> </a> <a href="https://grafana.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/grafana/grafana-icon.svg" alt="grafana" width="40" height="40"/> </a> <a href="https://www.w3.org/html/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/html5/html5-original-wordmark.svg" alt="html5" width="40" height="40"/> </a> <a href="https://www.adobe.com/in/products/illustrator.html" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/adobe_illustrator/adobe_illustrator-icon.svg" alt="illustrator" width="40" height="40"/> </a> <a href="https://www.jenkins.io" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/jenkins/jenkins-icon.svg" alt="jenkins" width="40" height="40"/> </a> <a href="https://kubernetes.io" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/kubernetes/kubernetes-icon.svg" alt="kubernetes" width="40" height="40"/> </a> <a href="https://www.linux.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/linux/linux-original.svg" alt="linux" width="40" height="40"/> </a> <a href="https://mariadb.org/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/mariadb/mariadb-icon.svg" alt="mariadb" width="40" height="40"/> </a> <a href="https://www.mongodb.com/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/mongodb/mongodb-original-wordmark.svg" alt="mongodb" width="40" height="40"/> </a> <a href="https://www.microsoft.com/en-us/sql-server" target="_blank" rel="noreferrer"> <img src="https://www.svgrepo.com/show/303229/microsoft-sql-server-logo.svg" alt="mssql" width="40" height="40"/> </a> <a href="https://www.mysql.com/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/mysql/mysql-original-wordmark.svg" alt="mysql" width="40" height="40"/> </a> <a href="https://www.nginx.com" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nginx/nginx-original.svg" alt="nginx" width="40" height="40"/> </a> <a href="https://nodejs.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nodejs/nodejs-original-wordmark.svg" alt="nodejs" width="40" height="40"/> </a> <a href="https://www.photoshop.com/en" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/photoshop/photoshop-line.svg" alt="photoshop" width="40" height="40"/> </a> <a href="https://www.postgresql.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/postgresql/postgresql-original-wordmark.svg" alt="postgresql" width="40" height="40"/> </a> <a href="https://www.python.org" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg" alt="python" width="40" height="40"/> </a> <a href="https://www.rabbitmq.com" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/rabbitmq/rabbitmq-icon.svg" alt="rabbitMQ" width="40" height="40"/> </a> <a href="https://reactjs.org/" target="_blank" rel="noreferrer"> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/react/react-original-wordmark.svg" alt="react" width="40" height="40"/> </a> <a href="https://www.vagrantup.com/" target="_blank" rel="noreferrer"> <img src="https://www.vectorlogo.zone/logos/vagrantup/vagrantup-icon.svg" alt="vagrant" width="40" height="40"/> </a> </p>

<p>&nbsp;<img align="center" src="https://github-readme-stats.vercel.app/api?username=sanjo-varghese&show_icons=true&locale=en" alt="sanjo-varghese" /></p>
