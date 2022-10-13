# How Amazon FSx for NetApp ONTAP works<a name="how-it-works-fsx-ontap"></a>

This topic describes the major features of FSx for ONTAP and how they work, along with important implementation details\.

**Topics**
+ [FSx for ONTAP file systems](#fsx-ontap-file-system)
+ [Data\-tiering](#data-tiering)
+ [Using Microsoft Active Directory](#windows-smb-auth)
+ [Accessing data stored on FSx for ONTAP file systems](#access-ontap-file-systems)
+ [How to work with FSx for ONTAP](#how-to-work-with-fsxontap)

## FSx for ONTAP file systems<a name="fsx-ontap-file-system"></a>

An FSx for ONTAP file system is composed of the following primary resources:
+ Storage virtual machines \(SVMs\)
+ Volumes

A file system can have one or more SVMs, and an SVM can have one or more volumes\. The following image shows the structure of FSx for ONTAP file systems, and the relationship of the primary resources\. The FSx for ONTAP file system on the left is the simplest file system, with one SVM and one volume\. The file system on the right has multiple SVMs, with some SVMs having multiple volumes\. File systems and SVMs each have multiple endpoints for management, with SVMs also having endpoints for data access\.

![\[Structure of FSx for ONTAP file systems.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/ontap-file-system-structure.png)

### FSx for ONTAP file systems<a name="file-system-resource"></a>



A file system is the primary resource in Amazon FSx \(analogous to an ONTAP cluster on\-premises\)\. You specify the solid state drive \(SSD\) storage capacity and throughput capacity for your file system, and choose a virtual private cloud \(VPC\) in which your file system is created\. Each file system has a management endpoint that you can optionally use to manage your data by using the NetApp ONTAP CLI or REST API\. If data\-tiering is enabled, data stored in a file system can be automatically tiered to lower\-cost storage\. 

When creating an FSx for ONTAP file system, you define the following properties:
+ **Deployment type** – The deployment type of your file system \(Multi\-AZ or Single\-AZ\)\. Single\-AZ file systems replicate your data and offer automatic failover within a single Availability Zone\. Multi\-AZ file systems provide added resiliency by also replicating your data and supporting failover across multiple Availability Zones in the same AWS Region\.
+ **Storage capacity** – The amount of SSD storage, up to 192 tebibytes \(TiB\)\.
+ **SSD IOPS** – By default, each gigabyte of SSD storage includes three SSD IOPS\. You can optionally provision additional SSD IOPS as needed\.
+ **Throughput capacity** – The sustained speed at which the file server can serve data\.
+ **Networking** – The VPC and subnets for the management and data access endpoints that your file system creates\. For a Multi\-AZ file system, you also define an IP address range and route tables\.
+ **Encryption** – The AWS Key Management Service \(AWS KMS\) key used to encrypt the file system data at rest\.
+ **Administrative access** – You can specify the password for the `fsxadmin` user\. You can use this user to administer the file system by using the NetApp ONTAP CLI and REST API\.

For more information, see [Managing FSx for ONTAP file systems](managing-file-systems.md)\.

#### File system endpoints<a name="manage-file-system"></a>

You can manage FSx for ONTAP file systems by using the NetApp ONTAP CLI or REST API\. You can also set up SnapMirror relationships between an Amazon FSx file system and another ONTAP deployment \(including another Amazon FSx file system\)\. Each FSx for ONTAP file system has the following file system endpoints that provide access to NetApp applications:
+ **Management** – Use this endpoint to access the NetApp ONTAP CLI over Secure Shell \(SSH\), or to use the NetApp ONTAP REST API with your file system\.
+ **Intercluster** – Use this endpoint when setting up replication using NetApp SnapMirror or caching using NetApp FlexCache\.

For more information, see [Managing FSx for ONTAP resources using NetApp applications](managing-resources-ontap-apps.md) and [Scheduled replication using NetApp SnapMirror](scheduled-replication.md)\.

### Storage virtual machines \(SVM\)<a name="svm-resource"></a>

A storage virtual machine \(SVM\) is an isolated file server with its own administrative credentials and endpoints for administering and accessing data\. When you access data on FSx for ONTAP, your clients and workstations access the endpoint for the SVM in which the data is stored\. Amazon FSx automatically creates a default SVM on your file system for you when you create a file system using the AWS Management Console\.

Each SVM is a virtual resource, meaning that the SVMs in your file system share your file system's storage and throughput capacity\. Each SVM is also an isolated file server\. Therefore, if you have multiple users or groups who need access to administer data on Amazon FSx, you can create a separate SVM for each user or group so that they can independently administer their data\. 

To limit the amount of throughput and IOPS that individual workloads can drive, you can also configure quality of service \(QoS\) policies within your file system\. Using QoS policies helps ensure that individual workloads don't interfere with the other users and groups on the same file system\. 

You can create additional SVMs on your file system at any time by using the AWS Management Console, AWS CLI, or Amazon FSx API and SDKs\.

When creating an SVM, you define the following properties:
+ The file system to which it belongs\.
+ The Microsoft Active Directory configuration – You can optionally join your SVM to a self\-managed Active Directory for authentication and access control of Windows and macOS clients\.
+ The root volume security style – Set the root volume security style \(Unix, NTFS, or Mixed\) to align with the type of clients that you're using to access your data within the SVM\.
+ The SvmAdminPassword, which is the password for the SVM's `vsadmin` user\.

#### SVM endpoints<a name="svm-endpoints"></a>

Each SVM has four endpoints that are used to access data or to manage the SVM using the NetApp ONTAP CLI or REST API:
+ `Nfs` – For connecting using the Network File System \(NFS\) protocol
+ `Smb` – For connecting using the Service Message Block \(SMB\) protocol \(if your SVM is joined to an Active Directory\)
+ `Iscsi` – For connecting using the Internet Small Computer Systems Interface \(iSCSI\) protocol
+ `Management` – For managing SVMs using the NetApp ONTAP CLI or API, or NetApp Cloud Manager

For more information, see [Managing FSx for ONTAP storage virtual machines](managing-svms.md)\.

The following table lists the maximum number of SVMs that you can create for a file system\. The maximum number depends on the amount of throughput capacity provisioned in megabytes per second \(MBps\) and the protocols used to access volumes\.


| Amount of throughput capacity \(MBps\) | Maximum number of SVMs per file system | 
| --- | --- | 
| 128 | 6 | 
| 256 | 6 | 
| 512 | 14 | 
| 1024 | 14 | 
| 2048 | 24 | 

### Volumes<a name="volume-resource"></a>

Volumes are isolated data containers in which your files, directories, or iSCSI logical units of storage \(LUNs\) are stored\. Volumes are thin\-provisioned, meaning that they consume storage capacity only for the data stored in them\. Each volume is created within one of the SVMs in your file system\. Volumes enable you to group data, set permissions, establish data tiering policies and more, on a volume\-by\-volume basis\.

You can create volumes by using the AWS Management Console, AWS CLI, the Amazon FSx API, or NetApp Cloud Manager\. You can also use your file system’s or SVM’s administrative endpoint to create, update, and delete volumes by using the NetApp ONTAP CLI or REST API\.

When you create a volume, you define the following properties:
+ The name of the volume\.
+ The size of the volume\.
+ The junction path, which is the location in the SVM's namespace where the volume is mounted\.
+ Whether to enable storage efficiency to use compression, deduplication, and compaction to reduce the amount of storage your data consumes\.
+ The volume security style \(Unix, NTFS, or Mixed\) to match the majority of clients that you expect to be accessing the volume\.
+ Whether to enable automatic data\-tiering and set which tiering policy to use\. For more information, see [Data\-tiering](#data-tiering)\.

You can create up to 500 volumes per file system\. For more information, see [Managing FSx for ONTAP volumes](managing-volumes.md)\.

A volume is considered full when it reaches 98% storage utilization\.

## Data\-tiering<a name="data-tiering"></a>

 Amazon FSx automatically and intelligently transitions data between the *primary* and the *capacity pool* storage tiers, based on your data access patterns\. The *primary tier* is the SSD storage capacity that you provision when you create an FSx for ONTAP file system, and is used for the active portion of your dataset\. The less\-frequently accessed data in your dataset is automatically transitioned to the *capacity pool tier*\. The capacity pool tier is a lower\-cost storage tier that automatically grows and shrinks with the amount of data tiered to it\.

With data\-tiering, you can achieve SSD levels of performance for your workload while paying for SSD storage for only a small fraction of your data\. As a result, you need to provision primary SSD storage only for the active portion of your dataset, with the rest of your data stored in lower\-cost capacity pool storage\.

**Note**  
We recommend not exceeding 80% storage capacity utilization of the file system's primary tier\. This ensures that data tiering functions properly and predictably, and that there is overhead available for additional data\. You can [increase the amount of SSD storage capacity](managing-storage-capacity.md) as needed at any time\.

Each volume in your FSx for ONTAP file system has a data\-tiering policy associated with it\. This tiering policy determines how the data within that volume is transitioned between the primary tier and the capacity pool tier\. You can choose from one of four tiering policies:


+ **Auto** moves cold user data blocks from the active file system and snapshot copies to the capacity pool tier\.

  If read by random reads, the cold data blocks in the capacity pool tier become hot and move to the primary storage tier\. If read by sequential reads, such as those associated with index and antivirus scans, the cold data blocks stay cold and do not move to the primary storage tier\.
+ **Snapshot\-only** moves user data blocks in volume snapshot copies that are not associated with the active file system to the capacity pool tier\.

  If read, cold data blocks in the capacity pool tier become hot and are moved to the primary storage tier\.
+ **All** moves all user data blocks from the active file system and snapshot copies to the capacity pool tier\.

  If read, cold data blocks in the capacity pool tier stay cold and are not written back to the primary storage tier\.
+ **None** keeps a volume's data in the primary storage tier, preventing it from being moved to the capacity pool tier\.

For the **Auto** and **Snapshot\-only** tiering policies, you can also specify a minimum cooling period\. The minimum cooling period sets the time that user data in a volume must remain inactive for the data to be considered cold and moved to the capacity pool tier\. The minimum cooling period, which applies to both snapshot and active file system data, ranges from 2 to 183 days\. For **Auto**, the default cooling period is 31 days; for **Snapshot\-only**, the default cooling period is 2 days\.

When you create a new volume, data\-tiering is not enabled by default\. For information about enabling data\-tiering on a volume, see [Managing FSx for ONTAP volumes](managing-volumes.md)\.

## Using Microsoft Active Directory<a name="windows-smb-auth"></a>

You can use your existing Microsoft Active Directory for user authentication and authorization\. To do so, you must join any SVMs that are hosting volumes that will be accessed by Windows or macOS SMB clients to your Active Directory\. You specify your Active Directory's configuration when you create an SVM\. 

When joining an SVM to your Active Directory, the following are the Active Directory properties that you must set:
+ **NetBiosName** – The name of the computer object that's created in your Active Directory for your SVM\.
+ **DnsIps** – The IP addresses \(up to three\) for your DNS servers\.
+ **DomainName** – The fully qualified domain name of the self\-managed Active Directory directory\.
+ **FileSystemAdministratorsGroup** – The name of the domain group whose members have administrative permissions on your SVM\.
+ **Credentials** – The user name and password for a service account in your self\-managed Active Directory domain that Amazon FSx uses to join to your Active Directory domain\.

For more information, see [Working with Microsoft Active Directory in FSx for ONTAP](ad-integration-ontap.md)\.

## Accessing data stored on FSx for ONTAP file systems<a name="access-ontap-file-systems"></a>

You can access the data on your FSx for ONTAP file systems by using the following: 
+ Amazon Elastic Compute Cloud \(Amazon EC2\)
+ Amazon Elastic Container Service \(Amazon ECS\)
+ Amazon Elastic Kubernetes Service \(Amazon EKS\)
+ Amazon WorkSpaces
+ Amazon AppStream 2\.0
+ Red Hat OpenShift Service on AWS \(ROSA\)
+ VMware Cloud on AWS
+ On\-premises clients

You can access an ONTAP volume from multiple Linux, Windows, or macOS clients simultaneously over the NFS \(v3, v4, v4\.1, v4\.2\) and SMB protocols\. You can also use the iSCSI protocol to access iSCSI LUNs\. For more information, see [Accessing data](supported-fsx-clients.md)\.

You can access your file system from clients within the VPC associated with your file system, or from on\-premises or a peered VPC\. For more information on how to access your file systems from on premises or a peered VPC, see [Accessing data](supported-fsx-clients.md)\.

## How to work with FSx for ONTAP<a name="how-to-work-with-fsxontap"></a>

There are several ways that you can interact with FSx for ONTAP\. You can manage your FSx for ONTAP file systems by using the following AWS and NetApp management applications and tools:
+ AWS management tools:
  + The AWS Management Console
  + The AWS Command Line Interface \(AWS CLI\)
  + The Amazon FSx API and SDKs
+ NetApp management tools:
  + NetApp Cloud Manager
  + The NetApp ONTAP CLI
  + The NetApp ONTAP REST API

### AWS Management Console<a name="fsx-console"></a>

The AWS Management Console is a simple web\-based user interface\. You can manage your Amazon FSx file systems from the console with no programming required\. To access the Amazon FSx console, sign in to the AWS Management Console, and then open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

### Amazon FSx command line interface \(CLI\)<a name="fsx-cli"></a>

You can use the AWS CLI to access the Amazon FSx API interactively\. To install the AWS CLI, see [Getting started with the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)\. To begin using the AWS CLI for Amazon FSx, see the [AWS Command Line Interface reference for Amazon FSx](https://docs.aws.amazon.com/cli/latest/reference/fsx/)\.

### Amazon FSx application programming interface \(API\)<a name="fsx-api"></a>

If you're a developer, you use the Amazon FSx API actions and data types to programmatically configure and manage Amazon FSx and its resources\. You can also use the API in one of the language\-specific AWS software development kits \(SDKs\)\. For more information, see the [Amazon FSx API reference](https://docs.aws.amazon.com/fsx/latest/APIReference/welcome.html)\.

For application development, we recommend that you use one of the AWS SDKs\. The AWS SDKs handle low\-level details such as authentication, retry logic, and error handling, so that you can focus on your application logic\. The AWS SDKs are available for a wide variety of languages\. For more information, see [Tools to Build on AWS](http://aws.amazon.com/tools/)\.

AWS also provides libraries, sample code, tutorials, and other resources to help you get started more easily\. For more information, see the [AWS Developer Center](http://aws.amazon.com/developer/)\.

### NetApp Cloud Manager<a name="netapp-cloudmanager"></a>

NetApp Cloud Manager provides a centralized user interface to manage, monitor, and automate ONTAP deployments in AWS and on premises\. For more information, see [Managing FSx for ONTAP resources using NetApp applications](managing-resources-ontap-apps.md)\.

### NetApp ONTAP CLI<a name="netapp-cli"></a>

You can use the NetApp ONTAP CLI to manage your Amazon FSx for NetApp ONTAP file systems\. The ONTAP CLI provides a command\-based view of the ONTAP management interface\. You enter commands at the cluster management endpoint as the `fsxadmin` user, or at the SVM management endpoint as the `vsadmin` user\. For more information, see [Managing FSx for ONTAP resources using NetApp applications](managing-resources-ontap-apps.md)\.

### NetApp ONTAP REST API<a name="netapp-api"></a>

You can use the NetApp ONTAP REST API to manage your FSx for ONTAP file systems\. To access the ONTAP REST API, you can use your file system's management endpoint or the management endpoint associated with any SVM\. For more information, see [Managing FSx for ONTAP resources using NetApp applications](managing-resources-ontap-apps.md)\.