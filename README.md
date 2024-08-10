# Optimizing EKS Cluster Cost using `Kubecost`

**Optimizing the cost of our EKS clusters is very important. If we don't manage the cost of our EKS clusters properly, it may lead to a huge bill at the month end.** So to avoid this we need to optimize the cost of our EKS cluster. To Optimize the cost of our EKS clusters, we need to monitor the cost of our EKS cluster continuously, But for monitoring the cost of the cluster we need some tool right? There are so many Open Source and third-party tools available to optimize the cost of the EKS cluster. In this article, we will discuss how we can optimize the cost of the EKS cluster using a tool called `Kubecost`.

## Prerequisites

- An AWS account with necessary permissions to create EKS clusters.
- A Linux, MacOS or Windows machine with `AWS CLI`, `eksctl`, `eksdemo` and `kubectl` installed.
    - [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
    - [Install eksctl](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html)
    - [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
    - [Install eksdemo](https://github.com/awslabs/eksdemo/releases/tag/v0.15.0)

## Architecture/Workflow Diagram

![Kubecost - EKS](https://github.com/user-attachments/assets/e2dbcf5c-6c2d-41e4-9401-5f351c121559)

## Usage

Refer the [Kubecost Guide Section](kubecost-guide.md) for detailed steps to install and use Kubecost on your EKS cluster.<br>
Article on Medium: [Kubecost Guide](https://medium.com/@mathesh-me)

## Contributing

If you have any suggestions or issues, feel free to raise an issue or create a pull request. Contributors are welcome to contribute to this repository.

## Author

- [Mathesh M](https://www.linkedin.com/in/mathesh-me/)
- Checkout my articles on [Medium](https://medium.com/@mathesh-me) for more content on Kubernetes and DevOps.