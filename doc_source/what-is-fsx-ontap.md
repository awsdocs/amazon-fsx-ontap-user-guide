# What is Amazon FSx for NetApp ONTAP?<a name="what-is-fsx-ontap"></a>



Amazon FSx for NetApp ONTAP is a fully managed service that provides highly reliable, scalable, high\-performing, and feature\-rich file storage built on NetApp's popular ONTAP file system\. FSx for ONTAP combines the familiar features, performance, capabilities, and API operations of NetApp file systems with the agility, scalability, and simplicity of a fully managed AWS service\.

FSx for ONTAP provides feature\-rich, fast, and flexible shared file storage that’s broadly accessible from Linux, Windows, and macOS compute instances running in AWS or on premises\. FSx for ONTAP offers high\-performance solid state drive \(SSD\) storage with submillisecond latencies\. With FSx for ONTAP, you can achieve SSD levels of performance for your workload while paying for SSD storage for only a small fraction of your data\.

Managing your data with FSx for ONTAP is easier because you can snapshot, clone, and replicate your files with the click of a button\. In addition, FSx for ONTAP automatically tiers your data to lower\-cost, elastic storage, lessening the need for you to provision or manage capacity\. 

FSx for ONTAP also provides highly available and durable storage with fully managed backups and support for cross\-Region disaster recovery\. To make it easier to protect and secure your data, FSx for ONTAP supports popular data security and antivirus applications\. 

For customers who use NetApp ONTAP on\-premises, FSx for ONTAP is an ideal solution to migrate, back up, or burst your file\-based applications from on\-premises to AWS without the need to change your application code or how you manage your data\.

As a fully managed service, FSx for ONTAP makes it easier to launch and scale reliable, high\-performing, and secure shared file storage in the cloud\. With FSx for ONTAP, you no longer have to worry about:
+ Setting up and provisioning file servers and storage volumes
+ Replicating data
+ Installing and patching file server software
+ Detecting and addressing hardware failures
+ Managing failover and failback
+ Manually performing backups

FSx for ONTAP also provides rich integration with other AWS services, such as AWS Identity and Access Management \(IAM\), Amazon WorkSpaces, AWS Key Management Service \(AWS KMS\), and AWS CloudTrail\.

**Topics**
+ [Features of FSx for ONTAP](#features-overview)
+ [Security and data protection](#security-considerations)
+ [Pricing for FSx for ONTAP](#pricing-for-fsx-ontap)
+ [FSx for ONTAP forums](#forums)
+ [Are you a first\-time Amazon FSx user?](#first-time-user)

## Features of FSx for ONTAP<a name="features-overview"></a>

With FSx for ONTAP, you get a fully managed file storage solution with:
+ Support for petabyte\-scale datasets in a single namespace
+ Multiple gigabytes per second \(GBps\) of throughput per file system
+ Multi\-protocol access to data using the Network File System \(NFS\), Server Message Block \(SMB\), and Internet Small Computer Systems Interface \(iSCSI\) protocols
+ Highly available and durable Multi\-AZ and Single\-AZ deployment options
+ Automatic data\-tiering that reduces storage costs by automatically transitioning infrequently accessed data to a lower\-cost storage tier based on your access patterns
+ Data compression, deduplication, and compaction to reduce your storage consumption
+ Support for NetApp's SnapMirror replication feature
+ Support for NetApp's on\-premises caching solutions: NetApp Global File Cache and FlexCache
+ Support for access and management using native AWS or NetApp tools and API operations
  + AWS Management Console, AWS Command Line Interface \(AWS CLI\), and SDKs
  + NetApp ONTAP CLI, REST API, and Cloud Manager
+ Support for the following data protection and security features:
  + Encryption of file system data and backups at rest using AWS KMS keys
  + Encryption of data in transit using SMB Kerberos session keys
  + On\-demand antivirus scanning
  + Authentication and authorization using Microsoft Active Directory
  + File access auditing

## Security and data protection<a name="security-considerations"></a>

Amazon FSx provides multiple levels of security and compliance to facilitate protecting your data\. It automatically encrypts data at rest in file systems and backups using keys that you manage in AWS Key Management Service \(AWS KMS\)\. You can also encrypt data in transit using Kerberos for NFS and SMB clients\. 

Amazon FSx has been assessed to comply with the following standards:
+ International Standards Organization \(ISO\)
+ Payment Card Industry Data Security Standard \(PCI DSS\)
+ System and Organization Controls \(SOC\) certifications
+ The Health Insurance Portability and Accountability Act of 1996 \(HIPAA\)

For more information, see [Data protection in Amazon FSx for NetApp ONTAP](data-protection.md)\.

Amazon FSx also provides the following levels of access control: 
+ At the file system level, Amazon FSx provides access control by using Amazon Virtual Private Cloud \(Amazon VPC\) security groups\. 
+ At the API level, Amazon FSx provides access control by using AWS Identity and Access Management \(IAM\) access policies\. 
+ To provide access control at the file and folder level, Amazon FSx supports Unix permissions, NFS access control lists \(ACLs\), and NTFS ACLs\. When you join Amazon FSx to an Active Directory, users who are accessing file systems can authenticate using their Active Directory credentials\. 

So that you can see the actions taken by users on your Amazon FSx resources, Amazon FSx integrates with AWS CloudTrail to monitor and log your Amazon FSx API calls\. For more information, see [Logging FSx for ONTAP API Calls with AWS CloudTrail](logging-using-cloudtrail-win.md)\.

Additionally, Amazon FSx protects your data with highly durable file system backups\. Amazon FSx performs automatic daily backups, and you can take additional backups at any point\. For more information, see [Protecting your data](protecting-data.md)\.

## Pricing for FSx for ONTAP<a name="pricing-for-fsx-ontap"></a>

You are billed for file systems based on the following categories:
+ SSD storage capacity \(per gigabtye\-month, or GB\-month\)
+ SSD IOPS that you provision above three IOPS/GB \(per IOPS\-month\)
+ Throughput capacity \(per megabytes per second \[MBps\]\-month\)
+ Capacity pool storage consumption \(per GB\-month\)
+ Capacity pool requests \(per read and write\)
+ Backup storage consumption \(per GB\-month\)

For more information about pricing and fees associated with the service, see [Amazon FSx for NetApp ONTAP pricing](http://aws.amazon.com/fsx/netapp-ontap/pricing/)\.

## FSx for ONTAP forums<a name="forums"></a>

If you encounter issues while using Amazon FSx, use the FSx for ONTAP discussion [forums](https://forums.aws.amazon.com/forum.jspa?forumID=402) to get answers\.



## Are you a first\-time Amazon FSx user?<a name="first-time-user"></a>

If you're a first\-time user of Amazon FSx, we recommend that you read the following sections in order:

1. If you're new to AWS, see [Setting up FSx for ONTAP](setting-up.md) to set up an AWS account\.

1. If you're ready to create your first Amazon FSx file system, follow the instructions in [Getting started with Amazon FSx for NetApp ONTAP](getting-started.md)\.

1. For information about performance, see [Amazon FSx for NetApp ONTAP performancePerformance](performance.md)\.

1. For Amazon FSx security details, see [Security in Amazon FSx for NetApp ONTAP](security.md)\.

1. For information about the Amazon FSx API, see the [Amazon FSx API Reference](https://docs.aws.amazon.com/fsx/latest/APIReference/Welcome.html)\.