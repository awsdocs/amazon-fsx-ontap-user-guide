# Mounting on Microsoft Windows clients<a name="attach-windows-client"></a>

This section describes how to access data in your FSx for ONTAP file system with clients running the Microsoft Windows operating system\. Review the following requirements, regardless of the type of client you are using\.

This procedure assumes that the client and the file system are located in the same VPC and AWS account\. If the client is located on\-premise or in a different VPC, AWS account, or AWS Region, this procedure also assumes that you've set up AWS Transit Gateway or a dedicated network connection using AWS Direct Connect or a private, secure tunnel using AWS Virtual Private Network\. For more information, see [Accessing data from outside the deployment VPC](access-environments.md#access-from-outside-deployment-vpc)\.

We recommend that you attach volumes to your Windows clients using the SMB protocol\.

## Prerequisites<a name="attach-win-prereqs"></a>

To access an ONTAP storage volume using a Microsoft Windows client, you have to satisfy the following prerequisites:
+ The SVM of the volume you are attaching must be joined to your organization's Active Directory\. For more information, see [Managing FSx for ONTAP storage virtual machines](managing-svms.md)\.
+ The volume you are attaching has a security style setting of `NTFS` or `mixed`\. For more information, see [Managing FSx for ONTAP volumes](managing-volumes.md)\.

**To attach an ONTAP volume on a Windows client using SMB**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Create or select an Amazon EC2 instance running Microsoft Windows that is in the same VPC as the file system, and joined to the same Microsoft Active Directory as the volume's SVM\.

   For more information on launching an instance, see [Step 1: Launch an instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Windows Instances*\.

   For more information about joining an SVM to an Active Directory, see [Managing FSx for ONTAP storage virtual machines](managing-svms.md)\.

1. Connect to your Amazon EC2 Windows instance\. For more information, see [Connecting to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open a command prompt\.

1. Run the following command\. Replace the following:
   + Replace `Z:` with any available drive letter\.
   + Replace `DNS_NAME` with the DNS name or the IP address of the SMB endpoint for the volume's SVM\.
   + `C$` is the default SMB share at the root of the SVM's namespace\. If you’ve created any SMB shares in your SVM, you can provide the SMB share name to mount instead of `C$`\. For more information about creating SMB shares, see [Creating SMB shares](create-smb-shares.md)\.

   ```
   net use Z: \\DNS_NAME\C$
   ```

   The following example uses sample values\.

   ```
   net use Z: \\corp.example.com\new_share
   ```

   You can also use the IP address of the SVM instead of its DNS name\.

   ```
   net use Z: \\198.51.100.5\new_share
   ```