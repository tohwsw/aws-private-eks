# aws-private-eks

By default, when you create an Amazon EKS cluster, the Kubernetes cluster endpoint is public. While it is accessible from the internet, access to the Kubernetes cluster endpoint is restricted by AWS Identity and Access Management (IAM) and Kubernetes role-based access control (RBAC) policies.

To achieve more security, you may need to configure the Kubernetes cluster endpoint to be private.  Changing your Kubernetes cluster endpoint access from public to private completely disables public access such that it can no longer be accessed from the internet.

This post discusses the different options for a private EKS setup and the necessary configurations to achieve them. All the examples have their Kubernetes cluster endpoint set to private.

## 1. Private EKS with NAT gateways

In this setup, the worker nodes are placed into private subnets. The workers shall reach the internet via NAT gateways, which are placed into public subnets. The cluster is managed via the bastion host containing kubectl and and aws-iam-authenticator. The security group of the EKS ENI should allow ingress traffic on port 443 from the Bastion host. 

![img1]

[img1]:https://github.com/tohwsw/aws-private-eks/blob/master/private-eks-nat.png

## 2. Private EKS with NAT gateways and Management VPC

In this setup, the bastion host is no longer inside the same VPC (which has the EKS ENI), but is placed in a  seperate Management VPC. The EKS VPC can communicate with the Management VPC via VPC peering or AWS Transit Gateway. However, the name of the Kubernetes cluster endpoint needs to be resolvable from the mangement VPC. For this purpose, Amazon EKS automatically advertises the private IP addresses of the private endpoints through the public DNS name for the API server. Clients (such as kubectl) that are configured through the AWS Command Line Interface (AWS CLI) aws eks update-kubeconfig command or eksctl use the public endpoint DNS name to resolve and connect to private endpoints through the peered VPC automatically. https://aws.amazon.com/premiumsupport/knowledge-center/eks-private-cluster-endpoint-vpc/

![img2]

[img2]:https://github.com/tohwsw/aws-private-eks/blob/master/private-eks-outsidebastion.png

## 3. Private EKS with Proxy

In this configuration, there are no NAT gateways and internet gateway for outbound traffic. Instead all traffic must go through Proxy(for traffic filtering and monitoring), which could be in a shared services VPC. This means that worker nodes will need to go through the proxy able to pull the images required and also communicate with EC2 and ECR. We will require to set up the following:

The HTTP proxy for Amazon EKS worker nodes can be automated via the link below
https://aws.amazon.com/premiumsupport/knowledge-center/eks-http-proxy-configuration-automation/


![img3]

[img3]:https://github.com/tohwsw/aws-private-eks/blob/master/private-eks-proxy.png

## 4. Private EKS with no internet

In this configuration, there are no NAT gateways and no proxies. The EKS setup is isolated.

You must create and add endpoints for the following:
- Amazon Elastic Container Registry (Amazon ECR)
- Amazon Simple Storage Service (Amazon S3)
- Amazon Elastic Compute Cloud (Amazon EC2)

For example, you can use the following endpoints:
- api.ecr.ap-southeast-1.amazonaws.com
- dkr.ecr.ap-southeast-1.amazonaws.com
- s3.amazonaws.com
- s3.ap-southeast-1.amazonaws.com
- ec2.ap-southeast-1.amazonaws.com

![img4]

[img4]:https://github.com/tohwsw/aws-private-eks/blob/master/private-eks-nonat.png
