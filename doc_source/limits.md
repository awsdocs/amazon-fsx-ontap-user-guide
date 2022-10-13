# Quotas<a name="limits"></a>

Following, you can find out about quotas when working with Amazon FSx for NetApp ONTAP\.

**Topics**
+ [Quotas that you can increase](#soft-limits)
+ [Resource quotas for each file system](#limits-ontap-resources-file-system)

## Quotas that you can increase<a name="soft-limits"></a>

Following are the quotas for Amazon FSx for NetApp ONTAP for each AWS account, per AWS Region, that you can increase\.


****  

| Resource | Default | Description | 
| --- | --- | --- | 
|  ONTAP file systems  |  100  |  The maximum number of Amazon FSx for NetApp ONTAP file systems that you can create in this account\.  | 
|  ONTAP SSD storage capacity  |  524,288  |  The maximum amount of SSD storage capacity \(in GiB\) for all Amazon FSx for NetApp ONTAP file systems that you can have in this account\.  | 
|  ONTAP throughput capacity  |  10,240  |  The maximum amount of throughput capacity \(in MBps\) for all Amazon FSx for NetApp ONTAP file systems that you can have in this account\.  | 
|  ONTAP SSD IOPS  |  1,000,000  |  The maximum amount of SSD IOPS for all Amazon FSx for NetApp ONTAP file systems that you can have in this account\.  | 
|  ONTAP backups  |  10,000  |  The maximum number of user\-initiated volume backups for all Amazon FSx for NetApp ONTAP file systems that you can have in this account\.  | 

**To request a quota increase**

1. Open the [AWS Support](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and then choose **Create case**\.

1. For **Create case**, choose **Account and billing support**\.

1. In the **Case details** panel make the following entries:
   + For **Type** choose **Account**\.
   + For **Category** choose **Other Account Issues**\.
   + For **Subject** enter **Amazon FSx for NetApp ONTAP service limit increase request**\.
   + Provide a detailed **Description** of your request, including:
     + The FSx quota that you want increased, and the value you want it increased to, if known\.
     + The reason why you are seeking the quota increase\.
     + The file system ID and region for each file system you are requesting an increase for\.

1. Provide your preferred **Contact options** and choose **Submit**\.

## Resource quotas for each file system<a name="limits-ontap-resources-file-system"></a>

Following are the quotas on Amazon FSx for NetApp ONTAP resources for each file system in an AWS Region\.


****  

| Resource | Limit per file system | 
| --- | --- | 
| Minimum SSD storage capacity | 1024 GiB | 
| Maximum SSD storage capacity\* | 192 TiB | 
| Minimum throughput capacity | 128 MBps | 
| Maximum throughput capacity | 2,048 MBps | 
| Maximum number of volumes | 500 | 
| Maximum number of SVMs |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/limits.html)  | 
| Maximum number of tags | 50 | 
| Maximum retention period for automated backups | 90 days | 
| Maximum retention period for user\-initiated backups | no retention limit | 

\* There's virtually no limit to how much data you can store in a file system's capacity pool tier\.