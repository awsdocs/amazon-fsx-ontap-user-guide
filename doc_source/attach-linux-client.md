# Mounting on Linux clients<a name="attach-linux-client"></a>

We recommend that the SVM volumes to which you are attaching Linux clients have a security style setting of `UNIX` or `mixed`\. For more information, see [Managing FSx for ONTAP volumes](managing-volumes.md)\.

**Note**  
By default, FSx for ONTAP NFS mounts are `hard` mounts\. To ensure a smooth failover in the event that one occurs, we recommend that you use the default `hard` mount option\.

**To mount an ONTAP volume on a Linux client**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Create or select an Amazon EC2 instance running Amazon Linux 2 that is in the same VPC as the file system\.

   For more information on launching an EC2 Linux instance, see [ Step 1: Launch an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your Amazon EC2 Linux instance\. For more information, see [ Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open a terminal on your EC2 instance using secure shell \(SSH\), and log in with the appropriate credentials\.

1. Create a directory on the EC2 instance for mounting the SVM volume as follows:

   ```
   sudo mkdir /fsx
   ```

1. Mount the volume to the directory you just created using the following command:

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

## Using /etc/fstab to mount automatically on instance reboot<a name="mount-fs-auto-mount-update-fstab"></a>

To automatically remount your FSx for ONTAP volume when an Amazon EC2 Linux instance reboots, use the `/etc/fstab` file\. The `/etc/fstab` file contains information about file systems\. The command `mount -a`, which runs during instance start\-up, mounts the file systems listed in `/etc/fstab`\.

**Note**  
FSx for ONTAP file systems do not support automatic mounting using `/etc/fstab` on Amazon EC2 Mac instances\.

**Note**  
Before you can update the `/etc/fstab` file of your EC2 instance, make sure that you already created your FSx for ONTAP file system\. For more information, see [Creating FSx for ONTAP file systems](managing-file-systems.md#creating-file-systems)\.

**To update the /etc/fstab file on your EC2 instance**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file for your SSH command\. To do this, use the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. To use PuTTY, install it and convert the \.pem file to a \.ppk file\.

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
   +  [Connecting to your Linux instance from Windows using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 

1. Create a local directory that will be used to mount the SVM volume\.

   ```
   sudo mkdir /fsx
   ```

1. Open the `/etc/fstab` file in an editor of your choice\.

1. Add the following line to the `/etc/fstab` file\. Insert a tab character between each parameter\. It should appear as one line with no line breaks\.

   ```
   svm-dns-name:volume-junction-path /fsx nfs nfsvers=version,defaults 0 0
   ```

   You can also use the IP address of volume's SVM\. The last three parameters indicate NFS options \(which we set to default\), dumping of file system and filesystem check \(these are typically not used so we set them to 0\)\.

1. Save the changes to the file\.

1. Now mount the file share using the following command\. The next time the system starts, the folder will be mounted automatically\.

   ```
   sudo mount /fsx
   sudo mount svm-dns-name:volume-junction-path
   ```

Your EC2 instance is now configured to mount the ONTAP volume whenever it restarts\.