# Step 2: Mounting your file system from an Amazon EC2 Linux instance<a name="getting-started-step2"></a>

You can mount your file system from an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\. This procedure uses an instance running Amazon Linux 2\.

**To mount your file system from Amazon EC2**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Create or select an Amazon EC2 instance running Amazon Linux 2 that is in the same virtual private cloud \(VPC\) as your file system\. For more information about launching an instance, see [ Step 1: Launch an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your Amazon EC2 Linux instance\. For more information, see [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open a terminal on your Amazon EC2 instance using secure shell \(SSH\), and log in with the appropriate credentials\.

1. Make a directory on your Amazon EC2 instance for the volume's mount point with the following command\.

   ```
   $ sudo mkdir /fsx
   ```

1. Mount your Amazon FSx for NetApp ONTAP file system to the directory that you created\. Use a `mount` command similar to the following example\. In the following example, replace each *user input placeholder* with your own information\. 

   ```
   sudo mount -t nfs -o nfsvers=4.1 svm-01234567.fs-01234567.fsx.us-east-1.amazonaws.com:/vol1 /fsx
   ```

If you have issues with your Amazon EC2 instance \(such as connections timing out\), see [Troubleshoot EC2 instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-troubleshoot.html) in the *Amazon EC2 User Guide for Linux Instances*\.