## MY TF MODULE TO PROVISION AN EKS CLUSTER WITH CUSTOM NETWORKING------->



## Sample usage:

~~~
module "eks-module" {
  source         = "github.com/ooghenekaro/my-eks-module"
  region         = "Enter your Providers region here"
  vpc_cidr       = "10.0.0.0/16"
  dns_hostnames  = true
  dns_support    = true
  pub_one_cidr   = "10.0.1.0/24"
  pub_two_cidr   = "10.0.2.0/24"
  priv_one_cidr  = "10.0.3.0/24"
  priv_two_cidr  = "10.0.4.0/24"
  az_one         = "Enter your first az"
  az_two         = "Enter your second az"
  vpc_id         = "aws_vpc.eks_vpc.id"
  eks_version    = "1.26"
  ami_type       = "AL2_x86_64"
  instance_types = ["t3.small", "t3.medium", "t3.large"]
  capacity_type  = "ON_DEMAND"

}

This should be provisioned within your cluster for the autoscaling config to work

  scaling_config {
    desired_size = var.desired_size
    max_size     = var.max_size
    min_size     = var.min_size
  }



~~~
After provisioning the cluster using the module above
then you authenticate into the cluster 

aws eks update-kubeconfig --name eks_cluster --region us-east-1
![Screenshot 2024-09-20 200514](https://github.com/user-attachments/assets/6f3d14c6-1bf6-431b-b198-c5fdbe8ae835)

