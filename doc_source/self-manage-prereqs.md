# Prerequisites for using a self\-managed Microsoft AD<a name="self-manage-prereqs"></a>

Before you create an FSx for ONTAP SVM joined to your self\-managed Microsoft AD domain, make sure that you have created and set up the following requirements:
+ An on\-premises or other self\-managed Microsoft AD that the SVM is to join, with the following configuration:
  + The domain functional level of your Active Directory domain controller is at Windows Server 2000 or higher\.
  + The DNS server IP addresses and Active Directory domain controller IP addresses\.
  +  Domain name that is not in the Single Label Domain \(SLD\) format\. Amazon FSx does not support SLD domains\. 
  + If you have Active Directory sites defined, you must make sure that the subnets in the VPC associated with your Amazon FSx file system are defined in the same Active Directory site, and that no conflicts exist between the subnets in your VPC and the subnets in your other sites\.
+ The following network configurations:
  + Connectivity configured between the Amazon VPC where you want to create the file system and your self\-managed Active Directory\. You can set up connectivity using AWS Direct Connect, AWS VPN, or AWS Transit Gateway\.
  + Ensure that the security group and the VPC Network ACLs for the subnet\(s\) where you're creating your FSx file system allow traffic on the ports and in the directions shown in the following diagram\.  
![\[FSx for ONTAP port configuration requirements for VPC security groups and network ACLs for the subnets where the SVM is being created.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/ontap-port-requirements.png)

    The following table identifies the role of each port\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/self-manage-prereqs.html)
  + Ensure that these traffic rules are also mirrored on the firewalls that apply to each of the Active Directory domain controllers, DNS servers, FSx clients and FSx administrators\.
**Important**  
While Amazon VPC security groups require ports to be opened only in the direction that network traffic is initiated, most Windows firewalls and VPC network ACLs require ports to be open in both directions\.
+  A service account in your self\-managed Microsoft AD with delegated permissions to join computers to the domain\. A *service account *is a user account in your self\-managed Microsoft AD that has been delegated certain tasks\. 

   The service account also needs to, at a minimum, be delegated the following permissions in the OU that you're joining the file system to: 
  + Ability to reset passwords
  + Ability to restrict accounts from reading and writing data
  + Validated ability to write to the DNS host name 
  + Validated ability to write to the service principal name 
  + Be delegated control to create and delete computer objects
  + Validated ability to read and write Account Restrictions

  These represent the minimum set of permissions that are required to join computer objects to your Active Directory\. For more information, see the Microsoft Windows Server documentation topic [ Error: Access is denied when non\-administrator users who have been delegated control try to join computers to a domain controller](https://support.microsoft.com/en-us/help/932455/error-message-when-non-administrator-users-who-have-been-delegated-con)\.

To learn more about creating a service account with the correct permissions, see [Delegating privileges to your Amazon FSx service account](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

**Note**  
Amazon FSx requires a valid service account throughout the lifetime of your Amazon FSx file system\. Amazon FSx must be able to fully manage the file system and perform tasks that require unjoining and rejoining your Active Directory domain using, such as replacing a failed file SVM or patching NetApp ONTAP software\. Keep your Active Directory configuration, including the service account credentials, updated with Amazon FSx\. To learn how, see [Keeping your Active Directory configuration updated with Amazon FSx](self-managed-AD-best-practices.md#keep-ad-config-updated)\.

 If this is your first time using AWS and FSx for ONTAP, make sure to set up before starting\. For more information, see [Setting up FSx for ONTAP](setting-up.md)\. 

**Important**  
Do not move computer objects that Amazon FSx creates in the OU after your SVMs are created or delete your Active Directory while your SVM is joined to it\. Doing so will cause your SVMs to become misconfigured\.