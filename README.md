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
OR
you could provision a cluster using this code on the terminal
eksctl create cluster --name <cluster-name> --region <region> --nodegroup-name <nodegroup-name> --nodes <number-of-nodes> --nodes-min <min-nodes> --nodes-max <max-nodes> --managed


Once you've provisioned your cluster, then deploy a sample app
into the cluster.
Within your manifest file, increase the number of replicaset to the
need for autoscalling. the default number of nodes within the cluster
provisioned is 2 which will not be sufficient if larger number of pods
are provisioned within a manifest file. With that an autoscaling manifest
will need to be deployed with this cluster.
kubectl apply -f (manifest name)
kubectl get pods (to see the pods that have been created)

This should be provisioned within your cluster for the autoscaling config to work

  scaling_config {
    desired_size = var.desired_size
    max_size     = var.max_size
    min_size     = var.min_size
  }

Then create cluster autoscaler file, ca.yml file , then copy and paste
the script from the lesson note.
this is a docker image file, just make sure the image version is suitable with
your version of kubernetes.
CHANGES TO MAKE IN THE SCRIPT
image:k8s.gcr.io/autoscaling/cluster-autoscaler:v1.26.2
node-group-auto-discover-asg:tag-k8s.io/cluster-autoscaler/enabled,k8s/cluster-autoscaler/(name of cluster)"eks_cluster"
NEXT
just apply
kubectl apply -f ca.yml
kubectl get pods -A
Now we've been able to deploy our autoscaler into our cluster
Now let deploy a sample application , to see what happens to our nodes.
https://github.com/medinahakinsowon/infra-jan24/blob/main/deployment.yml
Create deployment file (googlemicro.yml) and copy the deployment script and paste (increase the number of replicaset) then apply
kubectl apply -f googlemicro.yml
WHAT HAPPENS WITH THE CLUSTERAUTOSCALER
It will spin up more nodes for the deployment when we have more pods(replicaset)
and shut down the nodes once the replicaset reduces
~~~
After provisioning the cluster using the module above
then you authenticate into the cluster 

aws eks update-kubeconfig --name eks_cluster --region us-east-1
![Screenshot 2024-09-20 200514](https://github.com/user-attachments/assets/6f3d14c6-1bf6-431b-b198-c5fdbe8ae835)

