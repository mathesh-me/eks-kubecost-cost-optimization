# Optimizing EKS Cluster Cost using `Kubecost`

**Optimizing the cost of our EKS clusters is very important. If we don't manage the cost of our EKS clusters properly, it may lead to a huge bill at the month end.** So to vaoid this we need to optimize the cost of our EKS cluster. To Optimize the cost of the EKS cluster, we need to monitor the cost of the EKS cluster continuously. So we need a tool to monitor the cost of the EKS cluster. There are so many Open Source and third-party tools available to optimize the cost of the EKS cluster. In this article, we will discuss how we can optimize the cost of the EKS cluster using a tool called `Kubecost`.

## What is `Kubecost`?

`Kubecost` is a cost monitoring tool for Kubernetes clusters.**Kubecost provides real-time cost visibility and insights for teams using Kubernetes, helping you continuously reduce your cloud costs.** It is available as an open-source tool and also as a paid version.

You can find more about `Kubecost` [here](https://kubecost.com/).

Now, Let's see how we can optimize the cost of our EKS cluster using `Kubecost`.

## Architecture/Workflow Diagram

![Optimizing EKS Cluster Cost using Kubecost]()

## Prerequisites

- An AWS account with necessary permissions to create EKS clusters.
- A Linux, MacOS or Windows machine with `AWS CLI`, `eksctl`, and `kubectl` installed.
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

### Installing `Kubecost`

Before installing `Kubecost`, We need to understand some Prerequisites that `kubecost` depends on.<br>

If you look at the above picture, You can see that we need `EBS CSI Driver` and `Helm` to install `Kubecost` in the EKS cluster. So, Let's see how we can install `EBS CSI Driver` and `Helm` in the EKS cluster.<br>

Let's look at the most followed way of installing `EBS CSI Driver` and `Helm` in the EKS cluster:

- To install `Helm`, We need to run some commands. You can find the commands to install `Helm` [here](https://helm.sh/docs/intro/install/).
- Same goes with `EBS CSI Driver`. But for `EBS CSI Driver`, We need to create some IAM roles and policies by ourselves before installing the `EBS CSI Driver`. You can find the commands to install `EBS CSI Driver` [here](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). 

That's all some lots of manual work right? But we have a tool called `eksdemo` which will help us to install `EBS CSI Driver` and `Helm` in the EKS cluster. So, Let's see how we can install `EBS CSI Driver` and `Helm` in the EKS cluster using `eksdemo`.

- If we are using `eksdemo` for our EKS cluster, We don't need to worry about `Helm` because `eksdemo` comes with `Helm` pre-installed. It uses the `Helm` to manage application catalogs in the EKS cluster. So, We can directly jump into `EBS CSI Driver` and install it in our EKS cluster using `eksdemo`.

### Installing `EBS CSI Driver`

- Run the below command to install the `EBS CSI Driver` in the EKS cluster:

```bash
eksdemo install storage-ebs-csi -c <cluster-name>
```
This command will install the `EBS CSI Driver` in the EKS cluster. And it will also manage the IAM roles and policies for you. So, you don't need to worry about the IAM roles and policies. `eksdemo` will take care of that for you.

### Installing `Kubecost`

Now, we have installed the `EBS CSI Driver` in our EKS cluster. Let's see how we can install `Kubecost` in the EKS cluster.

- Run the below command to install the `Kubecost` in the EKS cluster:

```bash
eksdemo install kubecost-eks-amp -c <cluster-name> --node-exporter
```

This command will install the `Kubecost` in the EKS cluster with required Dependencies. Also, it will install the `Node Exporter` for you. In our case. `Node Exporter` is `Prometheus` so it will install the `Prometheus` for you. Because for collecting the metrics, `Kubecost` uses the `Prometheus`. If you already have the `Prometheus` installed in your EKS cluster, you can skip the `--node-exporter` flag.<br>

That command will also create one `Classic LoadBalancer` service for you. You can access your `Kubecost` dashboard using the `LoadBalancer` service URL.

- You can verify the installation by running the below command:

```bash
kubectl get pods -n kubecost
```

- You can access the `Kubecost` dashboard using the `LoadBalancer` service URL. You can get the `LoadBalancer` service URL by running the below command:

```bash
kubectl get svc -n kubecost
```

You can also get the `LoadBalancer` service URL from the `AWS Console`. You can navigate to the `EC2` service and then `Load Balancers` to get the `LoadBalancer` service URL.

![Screenshot]()

You have to copy the `LoadBalancer` service URL and open it in the browser with Port `9090`. You will see the `Kubecost` dashboard like below:

![Screenshot]()

### Utilizing `Kubecost` Dashboard

Once you open the `Kubecost` dashboard, You can see the cost of your EKS cluster in the dashboard. It also have lots of features like `Savings`, `Monitor`, `Alerts`, `Health` and `Govern`.<br> I cannot explain all the features here. But I will explain some of the important features here.

If you navigate to the `Savings` tab, You can see the potential savings of your EKS cluster. It will also show you some recommendations to save cost of your EKS cluster. You can follow the recommendations to save the cost of your EKS cluster.

You can also see the **Health** of your EKS cluster in the `Health` tab. It will show you the health of your EKS cluster like `CPU Utilization`, `Memory Utilization`, `Disk Utilization`, etc.


To Switch the Cluster, You can follow the below steps:


### Deleting the EKS Cluster

Once you are done with the `Kubecost` dashboard, You can delete the EKS cluster using the below command:

```bash
eksdemo delete cluster -c <cluster-name>
```

It will delete the EKS cluster and all the resources associated with the EKS cluster.

That's all about how we can optimize the cost of our EKS cluster using `Kubecost`. I hope you have learned something new from this article. If you have any queries or suggestions, feel free to reach out to me on [LinkedIn](https://www.linkedin.com/in/mathesh-me/). I am always happy to help you.