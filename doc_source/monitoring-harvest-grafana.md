# Monitoring FSx for ONTAP file systems using Harvest and Grafana<a name="monitoring-harvest-grafana"></a>

You can use standard NetApp monitoring tools to monitor your file system storage usage and performance, with the following Harvest and Grafana solution being one example\.

## Setting up a Harvest and Grafana environment<a name="harvest-grafana"></a>

You can monitor your Amazon FSx for NetApp ONTAP file system by using Harvest and Grafana\. NetApp Harvest monitors ONTAP datacenters by collecting performance, capacity, and hardware metrics from FSx for ONTAP file systems\. Grafana provides a dashboard where the collected Harvest metrics can be displayed\.

To get started, you can deploy an AWS CloudFormation template that automatically launches an Amazon EC2 instance running Harvest and Grafana\. As an input to the AWS CloudFormation template, you specify the `fsxadmin` user and the Amazon FSx management endpoint for the file system which will be added as part of this deployment\. After the deployment is completed, you can log in to the Grafana dashboard to monitor your file system\.

### AWS CloudFormation template<a name="harvest-grafana-template"></a>

This solution uses AWS CloudFormation to automate the deployment of the Harvest and Grafana solution\. The template creates an Amazon EC2 Linux instance and installs Harvest and Grafana software\. To use this solution, download the [fsx\-ontap\-harvest\-grafana\.template](https://solution-references.s3.amazonaws.com/fsx/harvest-grafana/harvest-grafana.yaml) AWS CloudFormation template\.

**Note**  
Implementing this solution incurs billing for the associated AWS services\. For more information, see the pricing details pages for those services\.

### Amazon EC2 instance types<a name="ec2-instance-types"></a>

When configuring the template, you provide the Amazon EC2 instance type\. NetApp's recommendation for the instance size depends on how many file systems you monitor and the number of metrics you choose to collect\. With the default configuration, for each 10 file systems you monitor, NetApp recommends:
+ CPU: 2 cores
+ Memory: 1 GB
+ Disk: 500 MB \(mostly used by log files\)

Following are some sample configurations and the `t3` instance type you might choose\.


****  

| File systems | CPU | Disk | Instance type | 
| --- | --- | --- | --- | 
|  Under 10  |  2 cores  |  500 MB  |  `t3.micro`  | 
|  10–40  |  4 cores  |  1000 MB  |  `t3.xlarge`  | 
|  40\+  |  8 cores  |  2000 MB  |  `t3.2xlarge`  | 

For more information on Amazon EC2 instance types, see [General purpose instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/general-purpose-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

#### Instance port rules<a name="instance-port-rules"></a>

When you set up your Amazon EC2 instance, make sure that ports 3000 and 9090 are open for inbound traffic for the security group that the Amazon EC2 Harvest and Grafana instance is in\.

### Deployment procedure<a name="harvest-grafana-deployment"></a>

The following procedure configures and deploys the Harvest/Grafana solution\. It takes about five minutes to deploy\. Before you start, you must have an FSx for ONTAP file system running in an Amazon Virtual Private Cloud \(Amazon VPC\) in your AWS account, and the parameter information for the template listed below\. For more information on creating a file system, see [Creating FSx for ONTAP file systems](managing-file-systems.md#creating-file-systems)\.

**To launch the Harvest/Grafana solution stack**

1. Download the [fsx\-ontap\-harvest\-grafana\.template](https://solution-references.s3.amazonaws.com/fsx/harvest-grafana/harvest-grafana.yaml) AWS CloudFormation template\. For more information on creating an AWS CloudFormation stack, see [Creating a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.
**Note**  
By default, this template launches in the US East \(N\. Virginia\) AWS Region\. You must launch this solution in an AWS Region where Amazon FSx is available\. For more information, see [Amazon FSx endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/fsxn.html) in the *AWS General Reference\. *

1. For **Parameters**, review the parameters for the template and modify them for the needs of your file system\. This solution uses the following default values\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/monitoring-harvest-grafana.html)

1. Choose **Next**\.

1. For **Options**, choose **Next**\.

1. For **Review**, review and confirm the settings\. You must select the check box acknowledging that the template create IAM resources\.

1. Choose **Create** to deploy the stack\.

You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in about five minutes\.

### Logging in to Grafana<a name="harvest-grafana-login"></a>

After the deployment has finished, use your browser to log in to the Grafana dashboard at the IP and port 3000 of the Amazon EC2 instance:

```
http://EC2_instance_IP:3000
```

When prompted, use the Grafana default user name \(`admin`\) and password \(`pass`\)\. We recommend that you change your password as soon as you log in\.

For more information, see the [ NetApp Harvest](https://github.com/NetApp/harvest) page on GitHub\.