# Optimizing EKS Cluster Cost using `Kubecost`

**Optimizing the cost of our EKS clusters is very important. If we don't manage the cost of our EKS clusters properly, it may lead to a huge bill at the month end.** So to avoid this we need to optimize the cost of our EKS cluster. To Optimize the cost of our EKS clusters, we need to monitor the cost of our EKS cluster continuously, But for monitoring the cost of the cluster we need some tool right? There are so many Open Source and third-party tools available to optimize the cost of the EKS cluster. In this article, we will discuss how we can optimize the cost of the EKS cluster using a tool called `Kubecost`.

## What is `Kubecost`?

`Kubecost` is a cost monitoring tool for Kubernetes clusters.**Kubecost provides real-time cost visibility and insights for teams using Kubernetes, helping you continuously reduce your cloud costs.** It is available as an open-source tool and also as a paid version.

You can find more about `Kubecost` [here](https://kubecost.com/).

Now, Let's see how we can optimize the cost of our EKS cluster using `Kubecost`.

## Architecture/Workflow Diagram

![Kubecost - EKS](https://github.com/user-attachments/assets/e2dbcf5c-6c2d-41e4-9401-5f351c121559)

## Prerequisites

- An AWS account with necessary permissions to create EKS clusters.
- A Linux, MacOS or Windows machine with `AWS CLI`, `eksctl`, `eksdemo` and `kubectl` installed.
    - [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
    - [Install eksctl](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html)
    - [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
    - [Install eksdemo](https://github.com/awslabs/eksdemo/releases/tag/v0.15.0)
- Knowledge of `eksdemo` tool is Important. You can read more about `eksdemo` in my previous article [here](). It's very user friemdly tool to create and manage EKS clusters. This tool is officially by AWS. You'll love it if you once try it because it have lots of cool features and very easy to use. I will also show one advantage of `eksdemo` in this article. I am sure you will definitely use this tool after reading this article.

## Steps to Optimize EKS Cluster Cost using `Kubecost`

Even though I don't want to explain the steps to get started with `eksdemo` here because I have already explained it in my previous article. But I need to include some of the basic steps like installation, Cluster creation and Application Catalog installation to get started with `Kubecost`. So, I will explain the steps to get started with `eksdemo` first and then I will explain the steps to install `Kubecost` in our EKS cluster.

### Installing `eksdemo`

Navigate to the [Releases page of eksdemo](https://github.com/awslabs/eksdemo/releases/tag/v0.15.0) and download the binary file based on your OS. For example, If you are using or Linux or Mac, You can download the binary file using `curl` or `wget` command.
![Screenshot 2024-08-07 114539](https://github.com/user-attachments/assets/cb859c70-7245-4410-97c5-9dd242ca3ba9)

#### For Linux:

```bash
wget https://github.com/awslabs/eksdemo/releases/download/v0.15.0/eksdemo_Linux_x86_64.tar.gz
```

Extract the tar file using the below command:

```bash
tar -xvf eksdemo_Linux_x86_64.tar.gz
```

Move the binary file to the `/usr/local/bin` directory.

```bash
sudo mv eksdemo /usr/local/bin/
```

![Screenshot 2024-08-07 152305](https://github.com/user-attachments/assets/acf2e5a1-dfbe-4b8b-b6bc-dfdcbf39ffe9)


Now, you can verify the installation by running the below command:

```bash
eksdemo version
```
For Mac, You can use te same method like above. And for Windows, You can download the binary file from the releases page and you can add the binary file to the PATH.

### Provisioning EKS Cluster

Now, we have installed the `eksdemo` tool. Let's see how we can provision the EKS Cluster using the `eksdemo` tool.

- Run the below command to create the EKS Cluster:

```bash
eksdemo create cluster -c <cluster-name>
```
![Screenshot 2024-08-07 152215](https://github.com/user-attachments/assets/392a493f-0d72-43ab-9709-55f80e3fec88)
![Screenshot 2024-08-07 152226](https://github.com/user-attachments/assets/6b4a8bb6-6735-4c94-954a-8851687622f8)
![Screenshot 2024-08-07 152236](https://github.com/user-attachments/assets/8bb307a6-14dd-4787-bc3e-710addfb2fb8)
![Screenshot 2024-08-07 152249](https://github.com/user-attachments/assets/e35ae981-0c0d-4d2b-949d-a65bd6b548f0)

The above command will create the EKS Cluster with the default configurations. Also one thing to mention, as I told earlier, `eksdemo` is built on top of `eksctl`. So behind the scenes, it will use the `eksctl` to create the EKS Cluster.

- You can verify the EKS Cluster creation by running the below command:

```bash
eksdemo get cluster
```
![Screenshot 2024-08-07 152520](https://github.com/user-attachments/assets/21b329c7-4861-44a9-8e03-62996642dec4)

The above command will show the EKS Cluster details in a user-friendly table format.

- You can also explore the `AWS Console` to see the EKS Cluster details and the resources provisioned for the EKS Cluster.
![Screenshot 2024-08-07 152609](https://github.com/user-attachments/assets/1c559f3e-a999-4a23-82d9-4f5e76fa376d)
![Screenshot 2024-08-07 152629](https://github.com/user-attachments/assets/4ee670d7-10a9-42ce-ba78-68d95c40574f)
![Screenshot 2024-08-07 152642](https://github.com/user-attachments/assets/a35bb7de-3b3d-4606-b456-07adf6284de9)

### Installing `Kubecost` and `EBS CSI Driver`

Before installing `Kubecost`, We need to understand some Prerequisites that `kubecost` depends on.<br>

[Screenshot]()

If you look at the above picture, You can see that we need `EBS CSI Driver` and `Helm` to install `Kubecost` in the EKS cluster. So, Let's see how we can install `EBS CSI Driver` and `Helm` in our EKS cluster.<br>

The most followed way of installing `EBS CSI Driver` and `Helm` in the EKS cluster:

- To install `Helm`, We need to run some commands. You can find the commands to install `Helm` [here](https://helm.sh/docs/intro/install/).
- Same goes with `EBS CSI Driver`. But for `EBS CSI Driver`, We need to create some IAM roles and policies by ourselves before installing the `EBS CSI Driver`. You can find the commands to install `EBS CSI Driver` [here](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). 

That's all some lots of manual work right? But we have a tool called `eksdemo` which will help us to install `EBS CSI Driver` and `Helm` in the EKS cluster. So, Let's see how we can install `EBS CSI Driver` and `Helm` in the EKS cluster using `eksdemo`.

- If we are using `eksdemo` for our EKS cluster, We don't need to worry about `Helm` because `eksdemo` comes with `Helm` pre-installed. It uses the `Helm` to manage application catalogs in the EKS cluster. So, We can directly jump into `EBS CSI Driver` and install it in our EKS cluster using `eksdemo`.

### Installing `EBS CSI Driver`

- Run the below command to install the `EBS CSI Driver` in the EKS cluster:

```bash
eksdemo install storage-ebs-csi -c <cluster-name>
```
![Screenshot 2024-08-07 154525](https://github.com/user-attachments/assets/eef3cae9-2da2-4d02-afce-70ce2461ec68)

This command will install the `EBS CSI Driver` in the EKS cluster. And it will also manage the IAM roles and policies for you. So, you don't need to worry about any IAM roles and policies. `eksdemo` will take care of that for you.

- You can check the installation using `kubectl get pods -n kube-system` Command.
![Screenshot 2024-08-07 154746](https://github.com/user-attachments/assets/efcfa6ad-5d3f-4643-ba7a-9f9af0aad0df)

### Installing `Kubecost`

Now, we have installed the `EBS CSI Driver` in our EKS cluster. Let's see how we can install `Kubecost` in the EKS cluster.

- Run the below command to install the `Kubecost` in the EKS cluster:

```bash
eksdemo install kubecost-eks-amp -c <cluster-name> --node-exporter
```
![Screenshot 2024-08-07 155027](https://github.com/user-attachments/assets/7d8cd869-0270-4afd-b76f-1df021f83a08)
![Screenshot 2024-08-07 155057](https://github.com/user-attachments/assets/b0e2163f-3a49-4d0b-8607-1a50dfc76901)


This command will install the `Kubecost` in the EKS cluster with required Dependencies. Also, it will install the `Node Exporter` for you. In our case, The `Node Exporter` is `Prometheus` so it will install the `Prometheus` for you. Because for collecting the metrics, `Kubecost` uses the `Prometheus` behind the scenes. If you already have the `Prometheus` installed in your EKS cluster, you can skip the `--node-exporter` flag.<br>

That command will also create one `Classic LoadBalancer` service for you. You can explore it in your console also.
![Screenshot 2024-08-07 160315](https://github.com/user-attachments/assets/ec3803a2-7f3a-4016-ad83-898950bdc03e)


- Now, You can verify the installation by running the below command:

```bash
kubectl get pods -n kubecost
```
![Screenshot 2024-08-07 155601](https://github.com/user-attachments/assets/5013cfc7-168e-4e2d-a0e9-f26b7bf65436)

- You can access the `Kubecost` dashboard using the `LoadBalancer` service URL. You can get the `LoadBalancer` service URL by running the below command:

```bash
kubectl get svc -n kubecost
```

![Screenshot 2024-08-07 155643](https://github.com/user-attachments/assets/f499d934-0a30-436c-88c7-d7145cb1f034)

You have to copy the `LoadBalancer` service URL and open it in the browser with Port `9090`. You will see the `Kubecost` dashboard like below:

![Screenshot 2024-08-07 155516](https://github.com/user-attachments/assets/cd8c8af1-4d21-4715-94fb-2a9d2cdb038f)

You can also get the `LoadBalancer` service URL from the `AWS Console`. You can navigate to the `EC2` service and then `Load Balancers` to get the `LoadBalancer` service URL.

- Another way of accessing your `kubecost` dashboard is using `kubectl port-forward --namespace kubecost deployment/kubecost-cost-analyzer 9090` command. By using this command we can access our `kubecost` dashboard by using `localhost:9090` address in our Host machine.

### Utilizing `Kubecost` Dashboard

Once you open up the `Kubecost` dashboard, You can see the cost of your EKS cluster in the dashboard. It also have lots of features like `Savings`, `Monitor`, `Alerts`, `Health` and `Govern`.<br> I cannot explain all the features here. But I will explain some of the important features here.

![Screenshot 2024-08-07 155718](https://github.com/user-attachments/assets/22d7470a-60b4-4839-b1eb-dcff3193bc31)
![Screenshot 2024-08-07 155734](https://github.com/user-attachments/assets/5c65d1cd-acea-43ec-9664-50f0907f40a9)
![Screenshot 2024-08-07 155746](https://github.com/user-attachments/assets/144fc5b3-3baa-4797-9545-0d5963e21a77)
![Screenshot 2024-08-07 155805](https://github.com/user-attachments/assets/0ffd199e-74c6-4de7-ac00-8d50e484b29b)

#### Savings
**If you navigate to `Savings` tab, You can see the potential savings of your EKS cluster. It will also show you some recommendations to save cost of your EKS cluster. You can follow the recommendations to save the cost of your EKS cluster.**

![Screenshot 2024-08-07 155821](https://github.com/user-attachments/assets/0ec64e23-e5d1-40ad-a1ec-77a2a5d06fa5)
![Screenshot 2024-08-07 155833](https://github.com/user-attachments/assets/9cf35f01-37a0-4cbd-bdd5-bcd4db3cbbe3)
![Screenshot 2024-08-07 155859](https://github.com/user-attachments/assets/c632468b-ffac-4cb8-b594-46003d48ed01)

#### Cluster Health
You can also see the **Health** of your EKS cluster in the `Health` tab. It will give you some score based on the health of your EKS cluster.
![Screenshot 2024-08-07 155919](https://github.com/user-attachments/assets/8a57fb4d-bcc9-433e-b1b5-e66049596a6d)

#### Switching Cluster
To Switch the Cluster, You can follow the below steps:
![Screenshot 2024-08-07 155946](https://github.com/user-attachments/assets/6f5f86e1-c625-4dda-8f09-6d45a2d1c86c)
![Screenshot 2024-08-07 155959](https://github.com/user-attachments/assets/5f1d0ee9-5f36-4cfa-b545-390b42b76fe9)

### Deleting the EKS Cluster

Once you are done with the `Kubecost` dashboard, You can delete the EKS cluster using the below command:

```bash
eksdemo delete cluster -c <cluster-name>
```
![Screenshot 2024-08-07 160653](https://github.com/user-attachments/assets/6b468584-9e70-41c9-b5ef-03fb650d4de4)
![Screenshot 2024-08-07 162554](https://github.com/user-attachments/assets/abcb4ac4-661e-43d7-9b57-81088dca3be3)

It will delete the EKS cluster and all the resources associated with the EKS cluster.

That's all about how we can optimize the cost of our EKS cluster using `Kubecost`. I hope you have learned something new from this article. If you have any queries or suggestions, feel free to reach out to me on [LinkedIn](https://www.linkedin.com/in/mathesh-me/). I am always happy to help you.