# Mounting iSCSI LUNs to a Windows client<a name="mount-iscsi-windows"></a>

The examples presented in these procedures use the following set up:
+ The iSCSI LUN that is getting mounted to a Windows host is already created\. For more information, see [Creating an iSCSI LUN](create-iscsi-lun.md)\.
+ The Microsoft Windows host that is mounting the iSCSI LUN is an Amazon EC2 instance running a Microsoft Windows Server 2019 Amazon Machine Image \(AMI\)\. It has VPC security groups configured to allow inbound and outbound traffic as described in [ File System Access Control with Amazon VPC ](limit-access-security-groups.md)\.

  You may be using a different Microsoft Windows AMI in your set up\.
+ The client and the file system are located in the same VPC and AWS account\. If the client is located in another VPC, you can use VPC peering or AWS Transit Gateway to grant other VPCs access to the iSCSI endpoints\. For more information, see [Accessing data from outside the deployment VPC](access-environments.md#access-from-outside-deployment-vpc)\.

  We recommend that the EC2 instance be in the same availability zone as your file system's preferred subnet, as shown in the following graphic\.

![\[Image showing an Amazon FSx for NetApp ONTAP file system with an iSCSI LUN and an Amazon EC2 instance located in the same availability zone as that of the file system's preferred subnet.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-iscsi-mnt-client.png)

**Topics**
+ [Configure iSCSI on the Windows client](#configure-iscsi-win-client)
+ [Configure iSCSI on the FSx for ONTAP file system](#configure-iscsi-on-ontap-win)
+ [Mount an iSCSI LUN on the Windows client](#configure-iscsi-on-fsx)

## Configure iSCSI on the Windows client<a name="configure-iscsi-win-client"></a>

1. Use Windows Remote Desktop to connect to the Windows client on which you want to mount the iSCSI LUN\. For more information, see [Connect to your Windows instance using RDP](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html#connect-rdp) in the *Amazon Elastic Compute Cloud User Guide*\.

1. Open a Windows PowerShell as an Administrator\. Use the following commands to enable iSCSI on your Windows instance and configure the iSCSI service to start automatically\.

   ```
   PS C:\> Start-Service MSiSCSI 
   PS C:\> Set-Service -Name msiscsi -StartupType Automatic
   ```

1. Retrieve the initiator name of your Windows instance\. You’ll use this value in configuring iSCSI on your FSx for ONTAP file system using the NetApp ONTAP CLI\.

   ```
   PS C:\> (Get-InitiatorPort).NodeAddress
   ```

   The system responds with the initiator port:

   ```
   iqn.1991-05.com.microsoft:ec2amaz-abc123d 
   ```

1. To enable your clients to automatically failover between your file servers, you need install `Multipath-IO` \(MPIO\) on your Windows instance\. Use the following command:

   ```
   PS C:\> Install-WindowsFeature Multipath-IO
   ```

1. Restart your Windows instance after the `Multipath-IO` installation has completed\. Keep your Windows instance open to perform steps for mounting the iSCSI LUN in a section that follows\.

## Configure iSCSI on the FSx for ONTAP file system<a name="configure-iscsi-on-ontap-win"></a>

1. Connect to the NetApp ONTAP CLI on the FSx for ONTAP file system on which you created the iSCSI LUN using the following command\. For more information, see [Using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#netapp-ontap-cli)\.

   ```
   ~$ ssh fsxadmin@your_management_endpoint_ip
   ```

1. Using the NetApp ONTAP CLI, create the initiator group, or `igroup`\. An initiator group maps to iSCSI LUNs and controls which initiators \(clients\) have access to LUNs\. Replace `host_initiator_name` with the initiator name from your Windows host that you retrieved in the previous procedure\.

   ```
   ::> lun igroup create -vserver svm_name -igroup igroup_name -initiator host_initiator_name -protocol iscsi -ostype windows
   ```

   Both `windows` and `windows_2008` are supported for the `-ostype` option, depending on your environment\.

   If you want to make the LUNs mapped to this `igroup` available to multiple hosts, you can specify multiple comma\-separated initiator names\. For more information, see [https://docs.netapp.com/us-en/ontap-cli-9111/lun-create.html#parameters](https://docs.netapp.com/us-en/ontap-cli-9111/lun-create.html#parameters) in the *NetApp ONTAP Documentation Center*\.

1. Confirm the `igroup` was created successfully using the following command:

   ```
   ::> lun igroup show
   ```

   The system responds as follows:

   ```
   Vserver    Igroup        Protocol OS Type  Initiators 
   ---------  ------------  -------- -------- ------------------------------------ 
   svm_name igroup_name iscsi    linux    iqn.1994-05.com.windows:abcdef12345
   ```

   With the `igroup` created, you are ready to create LUNs and map them to the `igroup`\.

1. This step assumes that you have already created an iSCSI LUN\. If you have not, see [Creating an iSCSI LUN](create-iscsi-lun.md) for step\-by\-step instructions to do so\.

   Create a LUN mapping from the LUN to your new `igroup`\.

   ```
   ::> lun mapping create -vserver svm_name -path /vol/vol_name/lun_name -igroup igroup_name -lun-id lun_id
   ```

1. Confirm that the LUN is created, online, and mapped with the following command:

   ```
   ::> lun show -path /vol/vol_name/lun_name 
   Vserver     Path                            State   Mapped   Type     Size 
   ---------   ------------------------------- ------- -------- -------- -------- 
   svm_name  /vol/vol_name/lun_name online   mapped   windows  10GB
   ```

   You are now ready to add the iSCSI target on your Windows instance\.

1. Retrieve the IP addresses of the `iscsi_1` and `iscsi_2` interfaces for your SVM using the following command:

   ```
   ::> network interface show -vserver svm_name
   ```

   ```
   Logical    Status     Network            Current       Current Is 
   Vserver     Interface  Admin/Oper Address/Mask       Node          Port    Home 
   ----------- ---------- ---------- ------------------ ------------- ------- ---- 
   svm_name 
               iscsi_1    up/up      172.31.0.143/20    FSxId0123456789abcdef8-01 
                                                                      e0e     true 
               iscsi_2    up/up      172.31.21.81/20    FSxId0123456789abcdef8-02 
                                                                      e0e     true 
               nfs_smb_management_1 
                          up/up      198.19.250.177/20  FSxId0123456789abcdef8-01 
                                                                      e0e     true 
   3 entries were displayed.
   ```

   In this example, the IP address of `iscsi_1` is `172.31.0.143` and `iscsi_2` is `172.31.21.81`\.

## Mount an iSCSI LUN on the Windows client<a name="configure-iscsi-on-fsx"></a>

1. On your Windows instance, open a PowerShell terminal as an Administrator\.

1. You will create a `.ps1` script that does the following:
   + Connects to each of your file system’s iSCSI interfaces\.
   + Adds and configures MPIO for iSCSI\.
   + Establishes 4 sessions for each iSCSI connection, which enables the client to drive up to 20 Gb/s \(2,500 MB/s\) of aggregate throughput to the iSCSI LUN\. For more information, see [ Amazon EC2 instance network bandwidth](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-instance-network-bandwidth.html) in the *Amazon Elastic Compute Cloud User Guide for Windows Instances*\.

   Copy the following set of commands into a file to create the `.psl` script\.
   + Replace `iscsi_1` and `iscsi_2` with the IP addresses you retrieved in the previous step\.
   + Replace `ec2_ip` with the IP address of your Windows instance\.

   ```
   #iSCSI IP addresses for Preferred and Standby subnets 
   $TargetPortalAddresses = @("iscsi_1","iscsi_2") 
                                       
   #iSCSI Initator IP Address (Local node IP address) 
   $LocaliSCSIAddress = "ec2_ip" 
                                       
   #Connect to FSx for NetApp ONTAP file system 
   Foreach ($TargetPortalAddress in $TargetPortalAddresses) { 
   New-IscsiTargetPortal -TargetPortalAddress $TargetPortalAddress 
   -TargetPortalPortNumber 3260 -InitiatorPortalAddress $LocaliSCSIAddress 
   } 
                                       
   #Add MPIO support for iSCSI 
   New-MSDSMSupportedHW -VendorId MSFT2005 -ProductId iSCSIBusType_0x9 
                                       
   #Establish iSCSI connection 
   1..4 | %{Foreach($TargetPortalAddress in $TargetPortalAddresses)
   {Get-IscsiTarget | Connect-IscsiTarget -IsMultipathEnabled $true -TargetPortalAddress 
   $TargetPortalAddress -InitiatorPortalAddress $LocaliSCSIAddress -IsPersistent $true} } 
                                       
   #Set the MPIO Policy to Round Robin 
   Set-MSDSMGlobalDefaultLoadBalancePolicy -Policy RR
   ```

1. Launch the Windows Disk Management application\. Open the Windows Run dialog box, and enter `diskmgmt.msc` and press **Enter**\. The Disk Management application opens\.  
![\[The Windows Disk Management window is displayed.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/DiskMgmt.png)

1. Locate the unallocated disk This is the iSCSI LUN\. In the example, Disk 1 is the iSCSI disk\. It is offline\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/GoOnline.png)

   Bring the volume online by placing the cursor over **Disk 1** and right\-click then choose **Online**\.
**Note**  
You can modify the storage area network \(SAN\) policy so that new volumes are automatically brought online\. For more information, see [ SAN policies](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/san) in the *Microsoft Windows Server Command Reference*\.

1. To initialize the disk, place the cursor over **Disk 1** right\-click, and choose **Initialize**\. The Initialize dialog appears\. Choose **OK** initialize the disk\.

1. Format the disk as you would normally\. After formatting is complete, the iSCSI drive appears as a usable drive on the Windows client\.