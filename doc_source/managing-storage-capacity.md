# Managing SSD storage capacity and provisioned IOPS<a name="managing-storage-capacity"></a>

When you need additional storage for the active portion of your dataset, you can increase the solid state drive \(SSD\) storage capacity of your Amazon FSx for NetApp ONTAP file system\. You can do so by using the Amazon FSx console, Amazon FSx API, or AWS Command Line Interface \(AWS CLI\)\.

You can also change the provisioned SSD IOPS for your file system, either when you increase SSD storage capacity or as an independent action\. To specify the amount of provisioned SSD IOPS for your file system, you use one of two IOPS modes:
+ If you want Amazon FSx to automatically scale your SSD IOPS, use **Automatic** mode\.
+ If you want to specify the number of SSD IOPS, use **User\-provisioned** mode\.

 For more information about these modes, see [Considerations when updating storage and IOPS](#scaling-considerations)\.

When you increase the SSD storage capacity of your Amazon FSx file system, the new capacity is available for use within minutes\. You will be billed for the new SSD storage capacity after it becomes available to you\. For more information about pricing, see [Amazon FSx for NetApp ONTAP Pricing](http://aws.amazon.com/fsx/netapp-ontap/pricing/)\.

After you increase your storage capacity, Amazon FSx runs a storage\-optimization process in the background to rebalance your data\. For most file systems, storage optimization takes a few hours, with minimal noticeable impact on your workload performance\.

You can track the progress of the storage\-optimization process at any time by using the Amazon FSx console, CLI, and API\. For more information, see [Monitoring storage capacity and IOPS updates](#monitoring-storage-capacity-increase)\.

**Topics**
+ [Considerations when updating storage and IOPS](#scaling-considerations)
+ [When to increase storage capacity](#when-to-modify-storage-capacity)
+ [How to update SSD storage capacity and provisioned IOPS](#increase-storage-capacity)
+ [Monitoring storage capacity and IOPS updates](#monitoring-storage-capacity-increase)
+ [Increasing SSD storage capacity dynamically](#automate-storage-capacity-increase)

## Considerations when updating storage and IOPS<a name="scaling-considerations"></a>

Here are a few important items to consider when modifying your SSD storage capacity and provisioned IOPS:
+ **Storage capacity increase only** – You can only *increase* the amount of SSD storage capacity for a file system; you cannot decrease the storage capacity\.
+ **Storage capacity minimum increase** – Each SSD storage capacity increase must be a minimum of 10 percent of the file system's current SSD storage capacity, up to the maximum allowed value of 196,608 gibibytes \(GiB\)\.
+ **Time between increases** – You can't make further SSD storage capacity increases on a file system until 6 hours after the last increase was requested, or until the storage\-optimization process has finished, whichever time is longer\.
+ **Provisioned IOPS modes** – For a provisioned IOPS change, you must specify a mode\. The two IOPS modes are the following:
  + **Automatic** mode – Amazon FSx automatically scales your SSD IOPS to maintain 3 provisioned SSD IOPS per GiB of storage capacity, up to 80,000 SSD IOPS per file system\.
  + **User\-provisioned** mode – You specify the number of SSD IOPS, which must be greater than or equal to 3 IOPS per GiB of storage capacity\. You can optionally provision a higher level of IOPS\. If you do so, you pay for the average IOPS provisioned above your included rate for the month, measured in IOPS\-months\. For information about pricing, see [Amazon FSx for NetApp ONTAP Pricing](http://aws.amazon.com/fsx/netapp-ontap/pricing/)\.

For information about how Amazon FSx handles concurrent SSD storage capacity and throughput capacity requests, see [How concurrent throughput and storage scaling requests are handled](managing-throughput-capacity.md#concurrent-throughput-and-storage-requests)\.

## When to increase storage capacity<a name="when-to-modify-storage-capacity"></a>

If you are running out of available primary tier storage, we recommend that you increase the storage capacity of your file system\. Running out of storage indicates that your primary tier is undersized for the active portion of your dataset\.

To monitor the amount of free storage available on the file system, use the file system\-level `StorageCapacity` and `StorageUsed` Amazon CloudWatch metrics\. You can create a CloudWatch alarm on a metric and get notified when it drops below a specific threshold\. For more information, see [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)\.

**Note**  
We recommend not exceeding 80% primary storage capacity utilization to ensure that data tiering functions properly, and that there is capacity available for additional data\.

## How to update SSD storage capacity and provisioned IOPS<a name="increase-storage-capacity"></a>

You can increase a file system's SSD storage capacity and modify your provisioned SSD IOPS by using the Amazon FSx console, the AWS CLI, or the Amazon FSx API\.

### To update SSD storage capacity and provisioned IOPS for a file system \(console\)<a name="increase-storage-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**\. In the **File systems** list, choose the FSx for ONTAP file system that you want to update SSD storage capacity and SSD IOPS for\.

1. Choose **Actions** > **Update storage capacity**\. Or, in the **Summary** section, choose **Update** next to the file system's **SSD storage capacity** value\.

   The **Update SSD storage capacity and IOPS** dialog box appears\.  
![\[Console image showing the Update SSD storage capacity and IOPS dialog box.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/update-storage-capacity-modal.png)

1. To increase SSD storage capacity, select **Modify storage capacity**\.

1. For **Input type**, choose one of the following: 
   + To enter the new SSD storage capacity as a percentage change from the current value, choose **Percentage**\.
   + To enter the new value in GiB, choose **Absolute**\.

1. Depending on the input type, enter a value for **Desired % increase**\.
   + For **Percentage**, enter the percentage increase value\. This value must be at least 10 percent greater than the current value\.
   + For **Absolute**, enter the new value in GiB, up to the maximum allowed value of 196,608 GiB\.

1. For **Provisioned SSD IOPS**, you have two options to modify the number of provisioned SSD IOPS for your file system:
   + If you want Amazon FSx to automatically scale your SSD IOPS to maintain 3 provisioned SSD IOPS per GiB of primary storage capacity \(up to a maximum of 80,000\), choose **Automatic**\.
   + If you want to specify the number of SSD IOPS, choose **User\-provisioned**\. Enter an absolute number of IOPS that is at least 3 times the amount of GiB of your primary storage tier, and less than or equal to 80,000\.

1. Choose **Update**\.

### To update SSD storage capacity and provisioned IOPS for a file system \(CLI\)<a name="increase-storage-cli"></a>

To update the SSD storage capacity and provisioned IOPS for an FSx for ONTAP file system, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
+ Set `--file-system-id` to the ID of the file system that you are updating\.
+ To increase your SSD primary storage capacity, set `--storage-capacity` to a value that is at least 10 percent greater than the current value\.
+ To modify your provisioned SSD IOPS, use the `--ontap-configuration DiskIopsConfiguration` property\. This property has two parameters, `Iops` and `Mode`:
  + If you want to specify the number of provisioned IOPS, use `Iops=number_of_IOPS` \(up to a maximum of 80,000\) and `Mode=USER_PROVISIONED`\. The IOPS value must be greater than or equal to 3 times the requested SSD storage capacity\. If you're not increasing the storage capacity, the IOPs value must be greater than or equal to 3 times the current SSD storage capacity\.
  + If you want Amazon FSx to automatically increase your SSD IOPS, use `Mode=AUTOMATIC` and don't use the `Iops` parameter\. Amazon FSx will automatically maintain 3 provisioned SSD IOPS per GiB of your primary storage capacity \(up to a maximum of 80,000\)\.

The following example requests an increase of 2000 GiB to the file system's SSD storage capacity\. It also requests 7000 provisioned SSD IOPS\.

```
aws fsx update-file-system \
    --file-system-id fs-0123456789abcdef0 \
    --storage-capacity 2000 \
    --ontap-configuration 'DiskIopsConfiguration={Iops=7000,Mode=USER_PROVISIONED}'
```

To monitor the progress of the update, use the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command\. Look for the `AdministrativeActions` section in the output\. 

For more information, see [AdministrativeAction](https://docs.aws.amazon.com/fsx/latest/APIReference/API_AdministrativeAction.html) in the *Amazon FSx for NetApp ONTAP API Reference*\.

## Monitoring storage capacity and IOPS updates<a name="monitoring-storage-capacity-increase"></a>

You can monitor the progress of an SSD storage capacity and IOPS update by using the Amazon FSx console, the API, or the AWS CLI\.

### Monitoring updates in the console<a name="monitor-storage-action-console"></a>

In the **Updates** tab on the **File system details** page for your FSx for ONTAP file system, you can view the 10 most recent updates for each update type\.

![\[Console screen shot showing the recent updates list for a file system.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fs-updates-panel.png)

For SSD storage capacity and IOPS updates, you can view the following information:

****Update type****  
Supported types are **Storage capacity**, **Mode**, and **IOPS**\. The **Mode** and **IOPS** values are listed for all storage capacity and IOPS scaling requests\. 

****Target value****  
The value that you specified to update the file system's SSD storage capacity or IOPS to\.

****Status****  
The current status of the update\. The possible values are as follows:  
+ **Pending** – Amazon FSx has received the update request, but has not started processing it\.
+ **In progress** – Amazon FSx is processing the update request\.
+ **Updated; Optimizing** – Amazon FSx has increased the file system's SSD storage capacity\. The storage\-optimization process is now rebalancing your data in the background\.
+ **Completed** – The update finished successfully\.
+ **Failed** – The update request failed\. Choose the question mark \(**?**\) to see details on why the request failed\.

****Progress %****  
Displays the progress of the storage\-optimization process as the percentage complete\.

****Request time****  
The time that Amazon FSx received the update action request\.

### Monitoring increases with the AWS CLI and API<a name="monitor-storage-action-cli-api"></a>

You can view and monitor file system SSD storage capacity increase requests by using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\. The `AdministrativeActions` array lists the 10 most recent update actions for each administrative action type\. When you increase a file system's SSD storage capacity, two `AdministrativeActions` actions are generated: a `FILE_SYSTEM_UPDATE` and a `STORAGE_OPTIMIZATION` action\. 

The following example shows an excerpt of the response of a `describe-file-systems` CLI command\. The file system has a pending administrative action to increase the SSD storage capacity to 2000 GiB and the provisioned SSD IOPS to 7000\.

```
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1586797629.095,
        "Status": "PENDING",
        "TargetFileSystemValues": {
            "StorageCapacity": 2000,
            "OntapConfiguration": {
                "DiskIopsConfiguration": {
                    "Mode": "USER_PROVISIONED",
                    "Iops": 7000
                }
             }
        }
    },
    {
        "AdministrativeActionType": "STORAGE_OPTIMIZATION",
        "RequestTime": 1586797629.095,
        "Status": "PENDING"
    }
]
```

Amazon FSx processes the `FILE_SYSTEM_UPDATE` action first, adding the new larger storage disks to the file system\. When the new storage is available to the file system, the `FILE_SYSTEM_UPDATE` status changes to `UPDATED_OPTIMIZING`\. The storage capacity shows the new larger value, and Amazon FSx begins processing the `STORAGE_OPTIMIZATION` administrative action\. This behavior is shown in the following excerpt of the response of a `describe-file-systems` CLI command\. 

The `ProgressPercent` property displays the progress of the storage\-optimization process\. After the storage\-optimization process has completed successfully, the status of the `FILE_SYSTEM_UPDATE` action changes to `COMPLETED`, and the `STORAGE_OPTIMIZATION` action no longer appears\.

```
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1586799169.445,
        "Status": "UPDATED_OPTIMIZING",
        "TargetFileSystemValues": {
            "StorageCapacity": 2000,
            "OntapConfiguration": {
                "DiskIopsConfiguration": {
                    "Mode": "USER_PROVISIONED",
                    "Iops": 7000
                }
            }
        }
    },
    {
        "AdministrativeActionType": "STORAGE_OPTIMIZATION",
        "ProgressPercent": 41,
        "RequestTime": 1586799169.445,
        "Status": "IN_PROGRESS"
    }
]
```

 If the storage capacity or IOPS update request fails, the status of the `FILE_SYSTEM_UPDATE` action changes to `FAILED`, as shown in the following example\. The `FailureDetails` property provides information about the failure\.

```
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1586373915.697,
        "Status": "FAILED",
        "TargetFileSystemValues": {
            "StorageCapacity": 2000,
            "OntapConfiguration": {
                "DiskIopsConfiguration": {
                    "Mode": "USER_PROVISIONED",
                    "Iops": 7000
                }
            }
        },
        "FailureDetails": {
            "Message": "failure-message"
        }
    }
]
```

## Increasing SSD storage capacity dynamically<a name="automate-storage-capacity-increase"></a>

You can use the following solution to dynamically increase the SSD storage capacity of an FSx for ONTAP file system when the amount of used SSD storage capacity exceeds a threshold that you specify\. This AWS CloudFormation template automatically deploys all the components that are required to define the storage capacity threshold, the Amazon CloudWatch alarm based on this threshold, and the AWS Lambda function that increases the file system’s storage capacity\.

The solution automatically deploys all the components needed, and takes the following parameters:
+ Your FSx for ONTAP file system ID\.
+ The used SSD storage capacity threshold \(numerical value\)\. This is the percentage at which the CloudWatch alarm will be triggered\.
+ The percentage by which to increase the storage capacity \(%\)\.
+ The email address used to receive scaling notifications\.

**Topics**
+ [Architecture overview](#storage-inc-architecture)
+ [AWS CloudFormation template](#storage-capacity-CFN-template)
+ [Automated deployment with AWS CloudFormation](#fsx-dynamic-storage-increase-deployment)

### Architecture overview<a name="storage-inc-architecture"></a>

Deploying this solution builds the following resources in the AWS Cloud\.

![\[Architecture diagram of the solution to automatically increase the storage capacity of an FSx for ONTAP file system.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/dynamic-storage-scaling-architecture.png)

The diagram illustrates the following steps:

1. The AWS CloudFormation template deploys a CloudWatch alarm, an AWS Lambda function, an Amazon Simple Notification Service \(Amazon SNS\) queue, and all required AWS Identity and Access Management \(IAM\) roles\. The IAM role gives the Lambda function permission to invoke the Amazon FSx API operations\.

1. CloudWatch triggers an alarm when the file system’s used storage capacity exceeds the specified threshold, and sends a message to the Amazon SNS queue\. An alarm is triggered only when the file system’s used capacity exceeds the threshold continuously for a 5\-minute period\.

1. The solution then triggers the Lambda function that is subscribed to this Amazon SNS topic\.

1. The Lambda function calculates the new file system storage capacity based on the specified percent increase value and sets the new file system storage capacity\.

1. The original CloudWatch alarm state and results of the Lambda function operations are sent to the Amazon SNS queue\.

To receive notifications about the actions that are performed as a response to the CloudWatch alarm, you must confirm the Amazon SNS topic subscription by following the link provided in the **Subscription Confirmation** email\.

### AWS CloudFormation template<a name="storage-capacity-CFN-template"></a>

This solution uses AWS CloudFormation to automate deploying the components that are used to automatically increase the storage capacity of an FSx for ONTAP file system\. To use this solution, download the [FSxOntapDynamicStorageScaling](https://solution-references.s3.amazonaws.com/fsx/DynamicScaling/FSxOntapDynamicStorageScaling.yaml) AWS CloudFormation template\.

The template uses the **Parameters** described as follows\. Review the template parameters and their default values, and modify them for the needs of your file system\.



**FileSystemId**  
No default value\. The ID of the file system for which you want to automatically increase the storage capacity\.

**LowFreeDataStorageCapacityThreshold**  
No default value\. Specifies the used storage capacity threshold at which to trigger an alarm and automatically increase the file system's storage capacity, specified in percentage \(%\) of the file system's current storage capacity\. The file system is considered to have low free storage capacity when the used storage exceeds this threshold\.

**EmailAddress**  
No default value\. Specifies the email address to use for the SNS subscription and will receive the storage capacity threshold alerts\.

**PercentIncrease**  
Default is **20%**\. Specifies the amount by which to increase the storage capacity, expressed as a percentage of the current storage capacity\.  
Storage scaling will be attempted once every time the CloudWatch alarm enters the `ALARM` state\. If your SSD storage capacity utilization remains above the threshold after a storage scaling operation is attempted, the storage scaling operation will not be attempted again\.

**MaxFSxSizeinGiB**  
Default is **196608**\. Specifies the maximum supported storage capacity for the SSD storage\.

### Automated deployment with AWS CloudFormation<a name="fsx-dynamic-storage-increase-deployment"></a>

The following procedure configures and deploys an AWS CloudFormation stack to automatically increase the storage capacity of an FSx for ONTAP file system\. It takes about 5 minutes to deploy\. For more information about creating a CloudFormation stack, see [Creating a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.

**Note**  
Implementing this solution incurs billing for the associated AWS services\. For more information, see the pricing details pages for those services\.

Before you start, you must have the ID of the Amazon FSx file system running in an Amazon Virtual Private Cloud \(Amazon VPC\) in your AWS account\. For more information about creating Amazon FSx resources, see [Getting started with Amazon FSx for NetApp ONTAP](getting-started.md)\.

**To launch the automatic storage capacity increase solution stack**

1. Download the [FSxOntapDynamicStorageScaling](https://solution-references.s3.amazonaws.com/fsx/DynamicScaling/FSxOntapDynamicStorageScaling.yaml) AWS CloudFormation template\.
**Note**  
Amazon FSx is currently only available in specific AWS Regions\. You must launch this solution in an AWS Region where Amazon FSx is available\. For more information, see [Amazon FSx endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/fsxn.html) in the *AWS General Reference*\.

1. Go to the AWS CloudFormation console and choose **Create stack > With new resources**\.

1. Choose **Template is ready**\. In the **Specify template** section, choose **Upload a template file** and upload the template you downloaded\.

1. In **Specify stack details**, enter the values for your automatic storage capacity increase solution\.  
![\[Image showing the values entered for the Specify stack details page for the CloudFormation template.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/dynamic-storage-capacity-increase-cfn-stack.png)

1. Enter a **Stack name**\.

1. For **Parameters**, review the parameters for the template and modify them for the needs of your file system\. Then choose **Next**\.
**Note**  
To receive email notifications when scaling is attempted by this CloudFormation template, you need to confirm the SNS subscription email you receive after deploying this template\.

1. Enter any **Options** settings that you want for your custom solution, and then choose **Next**\.

1. For **Review**, review and confirm the solution settings\. You must select the check box acknowledging that the template creates IAM resources\.

1. Choose **Create** to deploy the stack\.

You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in about 5 minutes\.

#### Updating the stack<a name="automate-storage-capacity-increase-update"></a>

After the stack is created, you can update it by using the same template and providing new values for the parameters\. For more information, see [Updating stacks directly](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-direct.html) in the *AWS CloudFormation User Guide*\.