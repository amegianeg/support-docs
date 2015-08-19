## Which objects does Tutum create in my EC2 account?

The following objects will be created automatically by Tutum when deploying a node on AWS:

* An SSH keypair named `tutum-<uuid>`
* An EBS volume of type `gp2` with the size specified on the **Create new node cluster** wizard

By default, Tutum will try to deploy instances on EC2 using "EC2-VPC" ("EC2-Classic" is deprecated). It will create the following additional objects:

* An internet gateway named `tutum-gateway` attached to `tutum-vpc`
* A route table named `tutum-route-table` in `tutum-vpc` associated with `tutum-gateway` and `tutum-subnet`

There are three ways to create the rest of the objects:

### Leverage Tutum in order to create them automatically

Tutum will create them by default according to the following rules:

* A VPC is created with the tag name `tutum-vpc` and with the CIDR range `10.77.0.0/16`
* A set of subnets, if there are no subnets already created in the VPC. Tutum will create a subnet in every AZ possible, and will leave enough CIDR space for the user to create customized subnets. Every subnet created is tagged with the name `tutum-subnet`
* A VPC security group named `tutum-vpc-default` with all ports open by default

In order for Tutum to detect these objects after they are created, it's important that **their names don't change**. Otherwise, Tutum will try to recreate them and fail due to conflicts.

### Select them through UI or API

This gives you full control of the resources that Tutum will use to deploy your nodes, choosing the VPC, the subnet/s and the security group. You can do it through Tutum's user interface or [API](https://docs.tutum.co/v2/api/#create-a-new-node-cluster)

### Have them already created

You can create them in your AWS account with the same notation Tutum uses (name/tag name) and it will look for them and use. So, you can create:

* A VPC with the tag name `tutum-vpc`.
* A subnet/set of subnets in the `tutum-vpc`. Every subnet created must be tagged with the name `tutum-subnet`
* A VPC security group named `tutum-vpc-default`. See `Can I modify the tutum-default security group?` section below for more details. 


## Can I modify the tutum-vpc-default security group?

You can change the ports opened in the `tutum-vpc-default` security group as long as **ports 2375/tcp (docker), 6783/tcp + 6783/udp (weave)** are open. This is a temporary limitation that will be solved in the future.


## What happens if I restart a node in the AWS console?

After the node boots up, Tutum Agent will try contact our API and register itself with the new IP. We will update the DNS of the node and the containers on it to use the new IP automatically. The node should change its state from `Unreachable` to `Deployed`.

## Can I use an elastic IP for my nodes?

Yes, you can, but you will need to restart the Tutum Agent (or the host) for the changes to take effect in Tutum.


## Can I terminate a node from the AWS console?

If you created the node using Tutum and you terminate it in the AWS console, all data in that node will be destroyed, as the volume attached to it is set to destroy on node termination. If you haven't revoked your Tutum IAM user, we will detect the termination and will mark the node as `Terminated` on Tutum.

If you created the host yourself, added it to Tutum as a "Bring Your Own Node" and then terminated it, the node will stay in `Unreachable` state until you manually remove it.

## How does Tutum deploy a node cluster in a region?

Tutum deploys every node in the cluster in the selected region in the less-populated availability zone (subnet) per cluster. This way Tutum can provide a high level of availability for your services, having the nodes of a cluster spread among the availability zones in a region.

## What policy does the Tutum user need?


As is described in [Link your Amazon Web Services account](https://support.tutum.co/support/solutions/articles/5000224910-link-your-amazon-web-services-account), you need to apply the following policy to your Tutum user:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ec2:*",
        "iam:ListInstanceProfiles"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

It provides full access to EC2 and can list instance profiles to be chosen to deploy your nodes.
