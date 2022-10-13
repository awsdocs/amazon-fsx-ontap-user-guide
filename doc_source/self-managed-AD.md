# Joining Amazon FSx SVMs to a Microsoft Active Directory<a name="self-managed-AD"></a>

Your organization might manage identities and devices in an Active Directory \(on\-premises or in the cloud\)\. If so, you can join your Amazon FSx file system's SVMs directly to your existing Active Directory domain\. When you create a new FSx for ONTAP SVM in the console, choose **Join an Active Directory** under the **Active Directory** section\. Provide the following details for your self\-managed Active Directory: 
+ The NetBIOS name of the Active Directory computer object to create for your SVM\. The NetBIOS name cannot exceed 15 characters\.
+ The fully qualified domain name of your Active Directory\. The domain name cannot exceed 255 characters\.
**Note**  
Domain name must not be in the Single Label Domain \(SLD\) format\. Amazon FSx currently does not support SLD domains\.
+ Up to 3 IP addresses of the DNS servers for your domain\. 

  The DNS server IP addresses and Active Directory domain controller IP addresses can be in any IP address range, except:
  + IP addresses that conflict with Amazon Web Services\-owned IP addresses in that AWS Region\. For a list of AWS\-owned IP addresses by region, see the [AWS IP address ranges](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html)\. 
  + IP addresses in the following CIDR block range: 198\.19\.0\.0/16
+ User name and password for a service account on your Active Directory domain, for Amazon FSx to use to join the SVM to your AD domain\.
+ \(Optional\) The Organizational Unit \(OU\) in your domain in which you want your SVM to be joined\.
**Note**  
If you are joining your SVM to an AWS Directory Service Active Directory, you must provide the name of an OU that's within the default OU that Directory Service creates for your AWS \-related directory objects\. This is because Directory Service does not provide access to your AD's default `Computers` OU\. For example, if your AD domain is `example.com`, you can specify the following OU: `OU=Computers,OU=example,DC=example,DC=com`
+ \(Optional\) The domain group to which you want to delegate authority to perform administrative actions on your file system\. For example, this domain group might manage Windows SMB file shares, take ownership of files and folders, and so on\. If you don’t specify this group, Amazon FSx delegates this authority to the Domain Admins group in your AD domain by default\.

  For more information, see [Creating a storage virtual machine](managing-svms.md#creating-svms)\.

**Important**  
Amazon FSx only registers DNS records for an SVM if you are using Microsoft DNS as the default DNS service\. If you are using a third\-party DNS, you will need to manually set up DNS entries for your Amazon FSx SVMs after you create them\.
If you are using AWS Managed Microsoft AD, you need to specify a group such as AWS Delegated FSx Administrators, AWS Delegated Administrators, or a custom group with delegated permissions to the OU\.

 When you join your FSx for ONTAP SVM directly to your self\-managed Active Directory, your SVM resides in the same AD forest \(the top\-most logical container in an Active Directory configuration that contains domains, users, and computers\) and in the same AD domain as your users and existing resources \(including existing file servers\)\.