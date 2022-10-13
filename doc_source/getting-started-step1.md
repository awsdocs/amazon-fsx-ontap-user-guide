# Step 1: Create an Amazon FSx for NetApp ONTAP file system<a name="getting-started-step1"></a>

The Amazon FSx console has two options for creating a file system – a **Quick create** option and a **Standard create** option\. To rapidly and easily create an Amazon FSx for NetApp ONTAP file system with the service recommended configuration, use the **Quick create** option\.

The **Quick create** option creates a file system with a single storage virtual machine \(SVM\) and one volume\. The **Quick create** option configures this file system to allow data access from Linux instances over the Network File System \(NFS\) protocol\. After your file system is created, you can create additional SVMs and volumes as needed, including an SVM joined to an Active Directory to allow access from Windows and macOS clients over the Server Message Block \(SMB\) protocol\. 

For information about using the **Standard create** option to create a file system with a customized configuration, see [Creating FSx for ONTAP file systems](managing-file-systems.md#creating-file-systems)\.

**To create your file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the dashboard, choose **Create file system** to start the file system creation wizard\.

1. On the **Select file system type** page, choose **Amazon FSx for NetApp ONTAP**, and then choose **Next**\. The **Create ONTAP file system** page appears\. For **Creation method**, choose **Quick create**\.  
![\[Quick create file system details screen showing options for the file system name, SSD storage capacity, virtual private cloud (VPC), and storage efficiency features.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/create-ontap-fs-quick.png)

1. In the **Quick configuration** section, for **File system name \- optional**, enter a name for your file system\. It's easier to find and manage your file systems when you name them\. You can use a maximum of 256 Unicode letters, white space, and numbers, plus these special characters: **\+** **\-** \(hyphen\) **=** **\.** **\_** \(underscore\) **:** **/**

1. For **Deployment type** choose **Multi\-AZ** or **Single\-AZ**\. 
   +  **Multi\-AZ** file systems replicate your data and support failover across multiple Availablity Zones in the same AWS Region\. 
   + **Single\-AZ** file systems replicate your data and offer automatic failover within a single Availability Zone\. 

    For more information, see [Availability and durability](high-availability-AZ.md)\.

1. For **SSD storage capacity**, specify the storage capacity of your file system, in gibibytes \(GiBs\)\. Enter any whole number in the range of 1,024–196,608\.

   You can increase the amount of storage capacity as needed at any time after you create the file system\. For more information, see [Managing SSD storage capacity and provisioned IOPS](managing-storage-capacity.md)\.

1. For **Virtual Private Cloud \(VPC\)**, choose the Amazon VPC that you want to associate with your file system\.

1. For **Storage efficiency**, choose **Enabled** to turn on the ONTAP storage efficiency features \(compression, deduplication, and compaction\) or **Disabled** to turn them off\.

1. Choose **Next**\.

1. Review the file system configuration shown on the **Create ONTAP file system** page\. For your reference, note which file system settings you can modify after the file system is created\.

1. Choose **Create file system**\.

**Quick create** creates a file system with one SVM \(named `fsx`\) and one volume \(named `vol1`\)\. The volume has a junction path of `/vol1` and a capacity pool tiering policy of **Auto** \(which will automatically tier any data that hasn't been accessed for 31 days to lower\-cost capacity pool storage\)\. The file system data is encrypted at rest using your default service managed AWS KMS key\.