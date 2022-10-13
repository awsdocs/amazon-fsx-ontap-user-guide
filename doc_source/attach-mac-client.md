# Mounting on macOS clients<a name="attach-mac-client"></a>

This section describes how to access data in your FSx for ONTAP file system with clients running the macOS operating system\. Review the following requirements, regardless of the type of client you are using\.

This procedure assumes that the client and the file system are located in the same VPC and AWS account\. If the client is located on\-premise, or in a different VPC, AWS account or AWS Region, you've set up AWS Transit Gateway or a dedicated network connection using AWS Direct Connect or a private, secure tunnel using AWS Virtual Private Network\. For more information, see [Accessing data from outside the deployment VPC](access-environments.md#access-from-outside-deployment-vpc)\.

We recommend that you attach volumes to your Mac clients using the SMB protocol\.

**To mount an ONTAP volume on a macOS client using SMB**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Create or select an Amazon EC2 Mac instance running the macOS that is in the same VPC as the file system\.

   For more information on launching an instance, see [ Step 1: Launch an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-mac-instances.html#mac-instance-launch) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your Amazon EC2 Mac instance\. For more information, see [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open a terminal on your EC2 instance using secure shell \(SSH\), and log in with the appropriate credentials\.

1. Create a directory on the EC2 instance for mounting the volume as follows:

   ```
   sudo mkdir /fsx
   ```

1. Mount the volume using the following command\.

   ```
   sudo mount -t smbfs filesystem-dns-name:/smb-share-name mount-point
   ```

   The following example uses sample values\.

   ```
   sudo mount -t smbfs svm-01234567890abcde2.fs-01234567890abcde5.fsx.us-east-1.amazonaws.com:/C$ /fsx
   ```

   You can also use the SVM's IP address instead of its DNS name\.

   ```
   sudo mount -t smbfs 198.51.100.10:/C$ /fsx
   ```

   `C$` is the default SMB share that you can mount to see the root of the SVM's namespace\. If you’ve created any Server Message Block \(SMB\) shares in your SVM, provide the SMB share names instead of `C$`\. For more information about creating SMB shares, see [Creating SMB shares](create-smb-shares.md)\.

**To mount an ONTAP volume on a macOS client using NFS**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Create or select an Amazon EC2 instance running Amazon Linux 2 that is in the same VPC as the file system\.

   For more information on launching an EC2 Linux instance, see [ Step 1: Launch an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your Amazon EC2 Linux instance\. For more information, see [ Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Mount your FSx for ONTAP volume on the Linux EC2 instance by either using a user\-data script during instance launch, or by running the following commands:

   ```
   sudo mount -t nfs -o nfsvers=NFS_version svm-dns-name:/volume-junction-path /mount-point
   ```

   The following example uses sample values\.

   ```
   sudo mount -t nfs -o nfsvers=4.1 svm-01234567890abdef0.fs-01234567890abcdef1.fsx.us-east-1.amazonaws.com:/vol1 /fsxontap
   ```

   You can also use the SVM's IP address SVM instead of it's DNS name\.

   ```
   sudo mount -t nfs -o nfsvers=4.1 198.51.100.1:/vol1 /fsxontap
   ```

1. Mount the volume to the directory you just created using the following command\.

   ```
   sudo mount -t nfs svm-dns-name:/volume-junction-path /fsx
   ```

   The following example uses sample values\.

   ```
   sudo mount -t nfs svm-01234567890abdef0.fs-01234567890abcdef1.fsx.us-east-1.amazonaws.com:/vol1 /fsx
   ```

   You can also use the SVM's IP address SVM instead of its DNS name\.

   ```
   sudo mount -t nfs 198.51.100.1:/vol1 /fsx
   ```