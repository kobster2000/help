# Create an Ocean Spark Cluster

There are several ways to deploy an Ocean Spark cluster:
- Create a new Kubernetes cluster from scratch
- Import an existing Kubernetes cluster to Ocean Spark
- Import an existing Ocean cluster to Ocean Spark

Each method is described below. Choose the method right for you.

## Create a New Kubernetes Cluster from Scratch
### Using spotctl

1. Install the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) (and configure it for your AWS account), the Kubernetes [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html) utility, and the [spotctl command-line tool](https://github.com/spotinst/spotctl#installation).

2. Create a cluster by running the command:

```
$ spotctl ocean spark create cluster --region $YOUR_REGION --cluster-name $MY_CLUSTER_NAME
```

This command will create a new EKS cluster, a new VPC, subnets, and other resources required to make Ocean Spark functional.

### Using Terraform

**Option 1**: Deploy Ocean Spark cluster in an existing VPC.
Follow [this example](https://github.com/spotinst/terraform-spotinst-ocean-spark/tree/main/examples/from-private-vpc) from the [ocean-spark Terraform module](https://registry.terraform.io/modules/spotinst/ocean-spark/spotinst/latest).

**Option 2**: Deploy Ocean Spark cluster in a new VPC.
Follow [this example](https://github.com/spotinst/terraform-spotinst-ocean-spark/tree/main/examples/from-scratch) from the ocean-spark Terraform module.

### Additional Method

You can also follow the documentation on how to get started with Ocean, and then use the method described below to import an existing Ocean cluster into Ocean Spark.

## Import an Existing Kubernetes Cluster to Ocean Spark
### Using Terraform

Use the ocean-spark Terraform module to import an existing EKS cluster into Ocean Spark.

### Additional Method

You can also follow the documentation on [how to get started with Ocean](ocean/getting-started/), and then use the method described below to import an existing Ocean cluster into Ocean Spark.

## Import an Existing Ocean Cluster to Ocean Spark

Ocean Spark leverages Ocean under the hood, so it’s easy to import an existing Ocean cluster into Ocean Spark. Running this step will install a few additional pods on your Ocean cluster. These pods will enable the features related to monitoring and optimization specific to Apache Spark.

### Using spotctl
1. Install the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) (and configure it for your AWS account), the Kubernetes [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html) utility, and the [spotctl command-line tool](https://github.com/spotinst/spotctl#installation).
2. Create a cluster by running this command, where the Ocean cluster ID is of the format o-XXXXXXXX:

```
$ spotctl ocean spark create cluster --cluster-id $YOUR_OCEAN_CLUSTER_ID
```

### Using Terraform

Follow [this example](https://github.com/spotinst/terraform-spotinst-ocean-spark/tree/main/examples/import-ocean-cluster) from the [ocean-spark Terraform module](https://registry.terraform.io/modules/spotinst/ocean-spark/spotinst/latest).

## Monitor your Ocean Spark Cluster Deployment

When you start running the script or command to create the cluster, the following major events take place:
1. Kubernetes cluster creation (If creating a cluster from scratch). The duration of this step varies depending on the cloud provider, but this can take 20 minutes or more. You may be able to track progress from your cloud provider console.
2. Ocean controller installation. The Ocean controller is installed on the cluster. The cluster is then registered with Spot and will be visible in the Spot console (under the Ocean UI).
3. Ocean Spark controller installation. The Ocean Spark components are then installed, and the cluster will be visible in the Spot console (under the Ocean Spark UI).

You can view the status of the newly created cluster on the Cluster page of the Ocean Spark console. The cluster status should move from Progressing to Available as the creation completes. Other statuses indicate an error. You can troubleshoot in the list of common issues below.

## Requirements for a Functioning Ocean Spark Cluster

This section provides a list of requirements for an Ocean Spark cluster deployment. The next section then puts those requirements in context by detailing common issues.

- The Kubernetes cluster should run one of Kubernetes versions 1.19, 1.20, or 1.21
- The VPC subnets should have the [proper tags](https://aws.amazon.com/premiumsupport/knowledge-center/eks-vpc-subnet-discovery/) to be discoverable by Kubernetes:
  - On all subnets: `kubernetes.io/cluster/<eks-cluster-name>: shared`
  - On public subnets: `kubernetes.io/role/elb: 1`
- The instance profile assumed by cluster nodes should have:
  - The [required permissions](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html) for EKS
- The permission to create security groups within the VPC
- The cluster nodes should be in a security groups that allows them:
  - To connect to one another
  - To reach the Internet
  - To connect to the Kubernetes API (which is in the cluster security group)
- If nodes are run in private subnets, make sure a NAT gateway is available in the cluster to enable egress to the Internet.
- All the Ocean Spark Virtual Node Groups (VNGs) should have access to the same subnets, or at least to the same availability zones (AZs).

## What’s Next?

Learn how to [submit your first Spark application](ocean-spark/getting-started/run-your-first-app).
