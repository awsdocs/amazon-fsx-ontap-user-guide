# Managing FSx for ONTAP file systems<a name="managing-file-systems"></a>

A *file system* is the primary FSx for ONTAP resource, analogous to a NetApp ONTAP cluster\. You create a file system in a particular virtual private cloud \(VPC\), and specify the file system's primary storage capacity and throughput capacity at creation\. If you enable data\-tiering, data stored in a file system can be transferred to lower\-cost storage\.

## Creating FSx for ONTAP file systems<a name="creating-file-systems"></a>

By default, when you create a new file system from the AWS Management Console, Amazon FSx automatically creates a file system with a single storage virtual machine \(SVM\) and one volume, allowing for quick access to data from Linux instances over the Network File System \(NFS\) protocol\. When creating the file system, you can optionally join the SVM to an Active Directory to enable access from Windows and macOS clients over the Server Message Block \(SMB\) protocol\. After your file system is created, you can create additional SVMs and volumes as needed\.

You can create an FSx for ONTAP file system using the Amazon FSx console, AWS CLI, or the Amazon FSx API\.

### To create a file system \(console\)<a name="create-MAZ-file-system-console"></a>

This procedure uses the **Standard create** creation option to create an FSx for ONTAP file system with a configuration that you customize for your needs\. For information about using the **Quick create** creation option to rapidly create a file system with a default set of configuration parameters, see [Step 1: Create an Amazon FSx for NetApp ONTAP file system](getting-started-step1.md)\.

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the dashboard, choose **Create file system** to start the file system creation wizard\.

1. On the **Select file system type** page, choose **Amazon FSx for NetApp ONTAP**, and then choose **Next**\. The **Create file system** page appears\.

1. For **Creation method**, choose **Standard create**\.

   Begin your configuration with the **File system details** section\.  
![\[Standard create File system basics section, showing options for file system parameters such as name, SSD storage capacity, and throughput capacity.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/create-ontap-fs-standard-basics.png)

1. For **File system name \- optional**, enter a name for your file system\. It's easier to find and manage your file systems when you name them\. You can use a maximum of 256 Unicode letters, white space, and numbers, plus these special characters: \+ \- = \. \_ : /

1. For **Deployment type** choose **Multi\-AZ** or **Single\-AZ**\. 
   + **Multi\-AZ** file systems replicate your data and support failover across multiple Availability Zones in the same AWS Region\.
   + **Single\-AZ** file systems replicate your data and offer automatic failover within a single Availability Zone\. 

    For more information, see [Availability and durability](high-availability-AZ.md)\.

1. For **SSD storage capacity**, enter the storage capacity of your file system, in gibibytes \(GiB\)\. Enter any whole number in the range of 1,024–196,608\.

   You can increase the amount of storage capacity as needed at any time after you create the file system\. For more information, see [Managing SSD storage capacity and provisioned IOPS](managing-storage-capacity.md)\.

1. For **Provisioned SSD IOPS**, you have two options to provision the number of IOPS for your file system:
   + Choose **Automatic** \(the default\) if you want Amazon FSx to automatically provision 3 IOPS per GiB of SSD storage\.
   + Choose **User\-provisioned** if you want to specify the number of IOPS\. You can provision a maximum of 80,000 SSD IOPS per file system\.

   You can increase your provisioned IOPS as needed at any time after you create the file system\. For more information, see [Managing SSD storage capacity and provisioned IOPS](managing-storage-capacity.md)\.

1. For **Throughput capacity**, choose a value that is your desired throughput capacity in MB per second \(MBps\)\. **Throughput capacity** is the sustained speed at which the file server that hosts your file system can serve data\. The **Recommended** value is based on the amount of storage capacity that you chose\. For more information, see [Amazon FSx for NetApp ONTAP performancePerformance](performance.md)\.

1. \(Multi\-AZ only\) In the **Networking** section, for **Virtual Private Cloud \(VPC\)**, choose the VPC that you want to associate with your file system\.

1. Specify a **Subnet** for your file server\. If you are creating a Multi\-AZ file system, also choose a **Standby subnet** for the standby file server\.

1. \(Multi\-AZ only\) For **VPC route tables**, specify the VPC route tables in which your file system's endpoints will be created\. Select all VPC route tables associated with the subnets in which your clients are located\. By default, Amazon FSx selects your VPC's default route table\.

1. \(Multi\-AZ only\) **Endpoint IP address range** specifies the IP address range in which the endpoints to access your file system will be created\.
**Important**  
The Endpoint IP address range you select for your file system must not conflict with, or must be a strict subset of \(i\.e\. must be more specific than\), any existing CIDR ranges in your VPC's route tables\. The maximum allowed size of the address range is /16\.

   You have two options to create the IP address range:
   + If you want Amazon FSx to choose an unused IP address range for you from the 198\.19\.0\.0/16 range, choose **No preference**\.
**Note**  
By default, FSx for ONTAP uses 198\.19\.0\.0/16 as the default endpoint IP address range for the file system\. This IP address range is also used by Amazon WorkSpaces for management traffic range, as described in [IP address and port requirements for WorkSpaces ](https://docs.aws.amazon.com/workspaces/latest/adminguide/workspaces-port-requirements.html) in the *Amazon WorkSpaces Administration Guide*\. If you intend to use WorkSpaces, you must select a non\-overlapping endpoint IP address range for your FSx for ONTAP file system\.
   + If you want to provide the address range, choose **Select an IP address range**\.
**Note**  
Do not choose any range that falls within the following CIDR ranges, as they are incompatible with FSx for ONTAP:  
0\.0\.0\.0/8
127\.0\.0\.0/8
198\.19\.0\.0/20
224\.0\.0\.0/4
240\.0\.0\.0/4
255\.255\.255\.255/32

1. In the **Security & encryption** section, for **Encryption key**, choose the AWS Key Management Service \(AWS KMS\) encryption key that protects your file system's data at rest\.

1. For **File system administrative password**, enter a secure password for the `fsxadmin` user\. Confirm the password\.

   You can use the `fsxadmin` user to administer your file system using the ONTAP CLI and REST API\. For more information about the `fsxadmin` user, see [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\.

1. In the **Default storage virtual machine configuration** section, provide the following information:
   + In the **Storage virtual machine name** field, provide a name for the storage virtual machine\. You can use a maximum of 47 alphanumeric characters, plus the underscore \(\_\) special character\.
   + For **SVM administrative password**, you can optionally choose **Specify a password** and provide a password for the SVM's `vsadmin` user\. You can use the `vsadmin` user to administer the SVM using the ONTAP CLI or REST API\. For more information about the `vsadmin` user, see [Managing SVMs using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#vsadmin-ontap-cli)\.

     If you choose **Don't specify a password** \(the default\), you can still use the file system's `fsxadmin` user to manage your file system using the ONTAP CLI or REST API, but you can't use your SVM's `vsadmin` user to do the same\.
   + In the **Active Directory** section, you can join an Active Directory to the SVM\. For more information, see [Working with Microsoft Active Directory in FSx for ONTAP](ad-integration-ontap.md)\.

     If you don't want to join your SVM to an Active Directory, choose **Do not join an Active Directory**\.

     If you want to join your SVM to a self\-managed Active Directory domain, choose **Join an Active Directory**, and provide the following details for your Active Directory:
     + The NetBIOS name of the Active Directory computer object to create for your SVM\. The NetBIOS name cannot exceed 15 characters\.
     + The fully qualified domain name of your Active Directory\. The domain name cannot exceed 255 characters\.
     + **DNS server IP addresses** – The IPv4 addresses of the Domain Name System \(DNS\) servers for your domain\.
     + **Service account username** – The user name of the service account in your existing Active Directory\. Do not include a domain prefix or suffix\.
     + **Service account password** – The password for the service account\.
     + **Confirm password** – The password for the service account\.
     + \(Optional\) **Organizational Unit \(OU\)** – The distinguished path name of the organizational unit to which you want to join your file system\.
     + **Delegated file system administrators group** – The name of the group in your Active Directory that can administer your file system\.

       If you are using AWS Managed Microsoft AD, you need to specify a group such as AWS Delegated FSx Administrators, AWS Delegated Administrators, or a custom group with delegated permissions to the OU\.

       If you are joining to a self\-managed AD, use the name of the group in your AD\. The default group is `Domain Admins`\.

1. In **Backup and maintenance \- *optional***, you can set the following options:
   + For **Daily automatic backup**, choose **Enabled** for automatic daily backups\. This option is enabled by default\.
   + For **Daily automatic backup window**, set the time of the day in Coordinated Universal Time \(UTC\) that you want the daily automatic backup window to start\. The window is 30 minutes starting from this specified time\. This window can't overlap with the weekly maintenance backup window\.
   + For **Automatic backup retention period**, set a period from 1–90 days that you want to retain automatic backups\.
   + For **Weekly maintenance window**, you can set the time of the week that you want the maintenance window to start\. Day 1 is Monday, 2 is Tuesday, and so on\. The window is 30 minutes starting from this specified time\. This window can't overlap with the daily automatic backup window\.

1. For **Tags \- *optional***, you can enter a key and value to add tags to your file system\. A tag is a case\-sensitive key\-value pair that helps you manage, filter, and search for your file system\.

   Choose **Next**\.

1. Review the file system configuration shown on the **Create file system** page\. For your reference, note which file system settings you can modify after the file system is created\.

1. Choose **Create file system**\.

### To create a file system \(CLI\)<a name="create-MAZ-file-system-cli"></a>
+ To create an FSx for ONTAP file system, use the [create\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html) CLI command \(or the equivalent [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html) API operation\), as shown in the following example\.

  ```
  aws fsx create-file-system \
      --file-system-type ONTAP \
      --storage-capacity 1024 \
      --storage-type SSD \
      --security-group-ids security-group-id \
      --subnet-ids subnet-id1 subnet-id2 \
      --ontap-configuration DeploymentType=Availablity-Zone-deployment-type,
          ThroughputCapacity=512,PreferredSubnetId=subnet-id1
  ```

After successfully creating the file system, Amazon FSx returns the file system's description in JSON format\.

**Note**  
Unlike the console file creation procedure, the `create-file-system` CLI command and the `CreateFileSystem` API operation don't create an SVM and a volume\. To create an SVM, see [Creating a storage virtual machine](managing-svms.md#creating-svms); to create a volume, see [Creating a volume](managing-volumes.md#creating-volumes)\.

## Updating a file system<a name="updating-file-system"></a>

You can update the configuration of an FSx for ONTAP file system using the Amazon FSx console, the AWS CLI, and the Amazon FSx API\.

### To update a file system \(console\)<a name="update-file-system-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, and then choose the ONTAP file system that you want to update\.

1. Choose a tab for the setting you want to update\. For example:
   + To change the weekly maintenance window or the ONTAP administrative password for the `fsxadmin` user, choose the **Administration** tab\.
   + To update the daily automatic backup window and the automatic backup retention period, choose the **Backups** tab\.

1. Choose **Update** next to a setting and provide the new information in the dialog box\.

### To update a file system \(CLI\)<a name="update-file-system-cli"></a>
+ To update the configuration of an FSx for ONTAP file system, use the [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command \(or the equivalent [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) API operation\), as shown in the following example\.

  ```
  aws fsx update-file-system \
      --file-system-id fs-0123456789abcdef0 \
      --ontap-configuration AutomaticBackupRetentionDays=30,DailyAutomaticBackupStartTime=01:00, \
        WeeklyMaintenanceStartTime=1:01:30,FSxAdminPassword=new-fsx-admin-password
  ```

## Deleting a file system<a name="delete-file-system"></a>

You can delete an FSx for ONTAP file system using the Amazon FSx console, the AWS CLI, and the Amazon FSx API and SDKs\.

**To delete a file system:**
+ **Using the console** – Follow the procedure described in [Step 3: Clean up resources](getting-started-step3.md)\.
+ **Using the CLI or API** – First delete all the volumes and SVMs on your file system\. Then use the [delete\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/delete-file-system.html) CLI command or the [DeleteFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DeleteFileSystem.html) API operation\.

## Viewing your file system<a name="viewing-file-system"></a>

You can view the details of your FSx for ONTAP file system using the Amazon FSx console, the AWS CLI, and the Amazon FSx API and SDKs\.

**To view a file system:**
+ **Using the console** – Choose a file system to view the **File systems** detail page\. The **Summary** panel shows the file system's ID, lifecycle status, deployment type, SSD storage capacity, throughput capacity, provisioned IOPS, Availability Zones, and creation time\.

  The tabs provide detailed information and configuration functions for the file system's features, such as backups, SVMs, and volumes\.
+ **Using the CLI or API **– Use the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) CLI command or the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

## FSx for ONTAP file system status<a name="file-system-lifecycle-states"></a>

You can view the status of an Amazon FSx file system by using the Amazon FSx console, the AWS CLI command [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html), or the API operation [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html)\.


| File system status  | Description | 
| --- | --- | 
|  AVAILABLE  |  The file system has been successfully created and is available for use\.  | 
|  CREATING  |  Amazon FSx is creating a new file system\.  | 
|  DELETING  |  Amazon FSx is deleting an existing file system\.  | 
|  MISCONFIGURED  |  The file system is in a misconfigured but recoverable state\.  | 
|  FAILED  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/managing-file-systems.html)  | 