# aws-private-eks

By default, when you create an Amazon EKS cluster, the Kubernetes cluster endpoint is public. While it is accessible from the internet, access to the Kubernetes cluster endpoint is restricted by AWS Identity and Access Management (IAM) and Kubernetes role-based access control (RBAC) policies.

To achieve more security, you may need to configure the Kubernetes cluster endpoint to be private.  Changing your Kubernetes cluster endpoint access from public to private completely disables public access such that it can no longer be accessed from the internet.

This post discusses the different options for a private EKS setup and the necessary configurations to achieve them.

## Private EKS with NAT gateways



![img1]

[img1]:https://github.com/tohwsw/aws-private-eks/blob/master/private-eks-nonat.png

