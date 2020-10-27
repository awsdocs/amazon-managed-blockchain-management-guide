# EMR Integration with EC2 Placement Groups<a name="emr-plan-ha-placementgroup"></a>

When you launch an Amazon EMR multiple master node cluster on Amazon EC2, you use placement groups to specify how you want instances to be deployed to protect against the effects of hardware failure\.

The placement group feature is supported for Amazon EMR versions 5\.23\.0 and later as an option for multiple master node clusters\. Currently, only master node types are supported by the placement group feature, and the `SPREAD` strategy is applied to those master nodes\. The `SPREAD` strategy places a small group of instances across separate underlying hardware to guard against the loss of multiple master nodes in the event of a hardware failure\. Note that an instance launch request can fail if there is insufficient unique hardware to fulfill the request\. For more information about EC2 placement strategies and limitations, see [Placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *EC2 User Guide for Linux Instances*\.

There is an initial limit of 500 placement groups that can be launched per AWS account\. Contact AWS support to request an increase in the number of allowed placement groups\. You can identify EC2 placement groups that EMR creates by tracking the key\-value pair tags added to the cluster and instance group associated with the EMR placement group\. For more information about EC2 cluster instance tags, see [View Cluster Instances in Amazon EC2](UsingEMR_Tagging.md)\.

## Launch an EC2 cluster with placement group strategy<a name="emr-plan-ha-launch-pg-strategy"></a>

To launch an Amazon EMR multiple master cluster within a placement group, you need to do both of the following:
+ Attach the placement group managed policy to the EMR role\. For more information, see [Attaching the placement group managed policy to the EMR role](#emr-plan-ha-launch-pg-policy)\.
+ Launch an Amazon EMR multiple master cluster with the `placement-group-configs` parameter using the EMR API or CLI\. For more information, see [Using the EMR API or CLI to enable the placement group feature](#emr-plan-ha-launch-pg-using-cli-api)\.

## Attaching the placement group managed policy to the EMR role<a name="emr-plan-ha-launch-pg-policy"></a>

The placement group feature requires a managed policy called `AmazonElasticMapReducePlacementGroupPolicy` in order to allow Amazon EMR to create, delete, and describe placement groups on EC2\. The managed policy must be attached to the EMR role before you launch the Amazon EMR cluster\. For more information, see [Service Role for Amazon EMR \(EMR Role\)](emr-iam-role.md)\.

The `AmazonElasticMapReducePlacementGroupPolicy` managed policy is the following JSON text that is created and administered by Amazon EMR\.

**Note**  
Because the `AmazonElasticMapReducePlacementGroupPolicy` managed policy is updated automatically, the policy shown here may be out\-of\-date\. Use the AWS Management Console to view the current policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Resource": "*",
            "Effect": "Allow",
            "Action": [
                "ec2:DeletePlacementGroup",
                "ec2:DescribePlacementGroups"
            ]
        },
        {
            "Resource": "arn:aws:ec2:*:*:placement-group/EMR_*",
            "Effect": "Allow",
            "Action": [
                "ec2:CreatePlacementGroup"
            ]
        }
    ]
}
```

## Using the EMR API or CLI to enable the placement group feature<a name="emr-plan-ha-launch-pg-using-cli-api"></a>

These examples demonstrate how to use the AWS CLI or API to launch master nodes of an HA cluster within a placement group\.

**Example – Launching an EMR cluster with a placement group using the EMR CLI\.**  
The following code example shows the CLI commands to launch an EMR cluster with a placement group\.  

```
aws emr create-cluster \
--name "ha-cluster" \
--placement-group-configs InstanceRole=MASTER \
--release-label emr-5.30.1 \
--instance-groups InstanceGroupType=MASTER,InstanceCount=3,InstanceType=m5.xlarge InstanceGroupType=CORE,InstanceCount=4,InstanceType=m5.xlarge \
--ec2-attributes KeyName=ec2_key_pair_name,InstanceProfile=EMR_EC2_DefaultRole,SubnetId=subnet-22XXXX01 \
--service-role EMR_DefaultRole \
--applications Name=Hadoop Name=Spark
```
+ Replace *subnet\-22XXXX01* with your subnet ID\.
+ Replace the *ec2\_key\_pair\_name* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.
+ Replace *ha\-cluster* with the name of your high\-availability cluster\.

**Example – Launching an EMR cluster with placement group strategy using the EMR API\.**  
When you use the RunJobFlow action to create a multiple master cluster, set the `PlacementGroupConfigs` property to the following\. Currently, the `MASTER` instance role automatically uses `SPREAD` as the placement group strategy\.  

```
{
	"Name": "ha-cluster",
	"PlacementGroupConfigs": [{"InstanceRole": "MASTER"}],
	"ReleaseLabel": "emr-5.30.1",
	"Instances": {
		"ec2SubnetId": "subnet-22XXXX01",
		"ec2KeyName": "ec2_key_pair_name",
		"InstanceGroups": [{
				"InstanceCount": 3,
				"InstanceRole": "MASTER",
				"InstanceType": "m5.xlarge"
			},
			{
				"InstanceCount": 4,
				"InstanceRole": "CORE",
				"InstanceType": "m5.xlarge"
			}
		]
	},
	"JobFlowRole": "EMR_EC2_DefaultRole",
	"ServiceRole": "EMR_DefaultRole"
}
```
+ Replace *subnet\-22XXXX01* with your subnet ID\.
+ Replace the *ec2\_key\_pair\_name* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.
+ Replace *ha\-cluster* with the name of your high\-availability cluster\.