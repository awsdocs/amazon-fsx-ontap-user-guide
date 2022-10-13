# You can't delete a storage virtual machine or volume<a name="cannot-delete-svm"></a>

**Topics**
+ [Identifying failed deletions](#identify-failed-deletions)
+ [SVM deletion: Route tables inaccessible](#deletion-route-tables)
+ [SVM deletion: Peer relationship](#deletion-peer-relationship)
+ [SVM or volume deletion: SnapMirror policies](#deletion-snapmirror)
+ [SVM deletion: Kerberos\-enabled LIF](#deletion-kerberos-lif)
+ [SVM deletion: Other reason](#deletion-ad-connectivity)
+ [Volume deletion: FlexCache relationship](#deletion-flexcache)

Each FSx for ONTAP file system can contain one or more storage virtual machines \(SVMs\), and each SVM can contain one or more volumes\. When you delete a resource, you must first ensure that all its children have been deleted\. For example, before deleting an SVM, you must first delete all the non\-root volumes in the SVM\.

**Important**  
 You can only delete storage virtual machines by using the Amazon FSx console, API, and CLI\. You can only delete volumes using the Amazon FSx console, API, or CLI if the volume has Amazon FSx backups enabled\.

To help protect your data and configuration, Amazon FSx prevents the deletion of SVMs and volumes in certain circumstances\. If you attempt to delete an SVM or volume, and your deletion request does not succeed, Amazon FSx provides you with information in the AWS console, AWS Command Line Interface \(AWS CLI\), and API regarding why the resource was not deleted\. After you have addressed the cause of the deletion failure, you can retry the deletion request\.

## Identifying failed deletions<a name="identify-failed-deletions"></a>

When you delete an Amazon FSx SVM or volume, you typically see the resource's `Lifecycle` state transition to `DELETING` for up to a few minutes before the resource disappears from the Amazon FSx console, CLI, and API\.

If you attempt to delete a resource and its `Lifecycle` state transitions from to `DELETING` and then back to `CREATED`, this behavior indicates that the resource did not successfully delete\. In this case, Amazon FSx reports an alert icon in the console next to the `CREATED` Lifecycle state\. Choosing the alert icon displays the reason for the unsuccessful deletion, as shown in the following example\.

![\[\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/troubleshoot-lifecycle-state.png)

The most common reasons why Amazon FSx prevents SVM and volume deletion are provided in the following sections, with step\-by\-step instructions on how to resolve these issues\.

## SVM deletion: Route tables inaccessible<a name="deletion-route-tables"></a>

Each FSx for ONTAP file system creates one or more route table entries to provide automatic failover and fail back across Availability Zones\. By default, these route table entries are created in your VPC's default route table\. You can optionally specify one or more non\-default route tables where FSx for ONTAP interfaces can be created\. Amazon FSx tags each route table that it associated with a file system with an `AmazonFSx` tag, and if this tag is removed, it can prevent Amazon FSx from being able to delete resources\. If this situation occurs, you see the following `LifecycleTransitionReason`: 

`Amazon FSx is unable to complete the requested storage virtual machine operation because of an inability to access one or more of the route tables associated with your file system. Please contact AWS Support.`

You can find your file system's route tables in the Amazon FSx console by navigating to the file system's summary page, under the **Network & security** tab: 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/troubleshoot-network-security.png)

Choosing the route tables link takes you to your route tables\. Next, verify that each of the route tables associated with your file system is tagged with this key\-value pair:

```
Key: AmazonFSx
Value: ManagedByAmazonFSx
```

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/troubleshoot-route-table-tags.png)

If this tag isn't present, recreate it, and then try to delete the SVM again\.

## SVM deletion: Peer relationship<a name="deletion-peer-relationship"></a>

If you're attempting to delete an SVM or volume that is part of a peer relationship, you must first delete the peer relationship before you delete the SVM or volume\. This requirement prevents the peered SVMs from becoming unhealthy\. If your SVM cannot be deleted because of a peer relationship, you see the following `LifecycleTransitionReason`:

Amazon FSx is unable to delete the storage virtual machine because it is part of a SVM peer or transition peer relationship\. Please delete the relationship and retry\.

You can delete SVM peer relationships through the ONTAP CLI\. To access the ONTAP CLI, follow the steps in [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. Using the ONTAP CLI, take the following steps\.

1. Check for SVM peer relationships by using the following command\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> vserver peer show -vserver svm_name
   ```

   If this command is successful, you'll see output similar to the following:

   ```
               Peer        Peer                           Peering        Remote
   Vserver     Vserver     State        Peer Cluster      Applications   Vserver
   ----------- ----------- ------------ ----------------- -------------- ---------
   svm_name    test2       peered       FsxId02d81fef0d84734b6
                                                          snapmirror     fsxDest
   svm_name    test3       peered       FsxId02d81fef0d84734b6
                                                          snapmirror     fsxDest
   2 entries were displayed.
   ```

1. Delete each SVM peer relationship by using the following command\. Replace `svm_name`, and `remote_svm_name` with your actual values\.

   ```
   FsxId123456789abcdef::> vserver peer delete -vserver svm_name -peer-vserver remote_svm_name
   ```

   If this command is successful, you'll see the following output:

   ```
   Info: 'vserver peer delete' command is successful.
   ```

## SVM or volume deletion: SnapMirror policies<a name="deletion-snapmirror"></a>

Just as you cannot delete an SVM with a peer relationship without first deleting the peer relationship \(see [SVM deletion: Peer relationship](#deletion-peer-relationship)\), you cannot delete an SVM that has a SnapMirror policy without first deleting the SnapMirror policy\. To delete the SnapMirror policy, use the ONTAP CLI to take the following steps\. To access the ONTAP CLI, follow the steps in [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. 

**Note**  
Amazon FSx backups use SnapMirror to create point\-in\-time, incremental backups of your file system's volumes\. You cannot delete this SnapMirror policy for your backups in the ONTAP CLI\. However, this policy is automatically deleted when you delete a volume through the AWS CLI, API, or console\. 

1. List your SnapMirror policies by using the following command\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> snapmirror policy show -vserver svm_name
   ```

   If this command is successful, you'll see output similar to the following:

   ```
   Vserver Policy             Policy Number         Transfer
   Name    Name               Type   Of Rules Tries Priority Comment
   ------- ------------------ ------ -------- ----- -------- ----------
   svm_name policy_name       async-mirror  1     8  normal  -
      Discard Configs: network
     SnapMirror Label: sm_created                         Keep:       1
                                                    Total Keep:       1
   ```

1. Delete your SnapMirror policy by using the following command\. Replace `svm_name`, and `policy_name` with your actual values\.

   ```
   FsxId123456789abcdef::> snapmirror policy delete -vserver svm_name -policy policy_name
   ```

## SVM deletion: Kerberos\-enabled LIF<a name="deletion-kerberos-lif"></a>

If you are attempting to delete an SVM that has a logical interface \(LIF\) with Kerberos enabled, you must first disable Kerberos on that LIF before deleting the SVM\.

You can disable Kerberos on a LIF through the ONTAP CLI\. To access the ONTAP CLI, follow the steps in [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\.

1. Enter diagnostic mode in the ONTAP CLI by using the following command\.

   ```
   FsxId123456789abcdef::> set diag
   ```

   When prompted to continue, enter **y**\.

   ```
   Warning: These diagnostic commands are for use by NetApp personnel only.
   Do you want to continue? {y|n}: y
   ```

1. Check which interfaces have Kerberos enabled\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> kerberos interface show -vserver svm_name
   ```

   If this command is successful, you'll see output similar to the following:

   ```
   (vserver nfs kerberos interface show)
                  Logical
   Vserver        Interface     Address         Kerberos SPN
   -------------- ------------- --------------- -------- -----------------------
   svm_name       nfs_smb_management_1
                                10.19.153.48    enabled  
   5 entries were displayed.
   ```

1. Disable the Kerberos LIF by using the following command\. Replace `svm_name` with the name of your SVM\. You'll need to provide the Active Directory username and password that you used to join this SVM to your Active Directory\.

   ```
   FsxId123456789abcdef::> kerberos interface disable -vserver svm_name -lif nfs_smb_management_1
   ```

   If this command is successful, you'll see the following output\. Provide the Active Directory username and password that you used to join this SVM to your Active Directory\. When prompted to continue, enter **y**\. 

   ```
   (vserver nfs kerberos interface disable)
   Username: admin
   Password: *************
   
   Warning: This command deletes the service principal name from the machine account on the KDC.
   Do you want to continue? {y|n}: y
        
   Disabled Kerberos on LIF "nfs_smb_management_1" in Vserver "svm_name".
   ```

1. Verify that Kerberos is disabled on the SVM by using the following command\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> kerberos interface show -vserver svm_name
   ```

   If this command is successful, you'll see output similar to the following:

   ```
   (vserver nfs kerberos interface show)
                  Logical
   Vserver        Interface     Address         Kerberos SPN
   -------------- ------------- --------------- -------- -----------------------
   svm_name       nfs_smb_management_1
                                10.19.153.48    disabled  
   5 entries were displayed.
   ```

1. If the interface is shown as `disabled`, try to delete the SVM again through the AWS CLI, API, or console\.

   If you weren't able to delete the LIF by using the preceding commands, you can force\-delete the Kerberos LIF by using the following command\. Replace `FsxId123456789abcdef` and `svm_name` with your actual values\.
**Important**  
The following command can strand the computer object of your SVM on your Active Directory\.

   ```
   FsxId123456789abcdef::> kerberos interface disable -vserver svm_name -lif nfs_smb_management_1 -force true
   ```

   If this command is successful, you'll see output similar to the following\. When prompted to continue, enter **y**\.

   ```
   (vserver nfs kerberos interface disable)
    
   Warning: Kerberos configuration for LIF "nfs_smb_management_1" in Vserver "svm_name" will be deleted.
   The corresponding account on the KDC will not be deleted. Do you want to continue? {y|n}: y
   ```

## SVM deletion: Other reason<a name="deletion-ad-connectivity"></a>

FSx for ONTAP SVMs create a computer object in your Active Directory when they join your Active Directory\. In some cases, you may want to manually unjoin an SVM from your Active Directory by using the ONTAP CLI\. To access the ONTAP CLI, follow the steps in [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli), logging into the ONTAP CLI at the file system level with `fsxadmin` credentials\. Using the ONTAP CLI, take the following steps to unjoin an SVM from your Active Directory\.

**Important**  
This procedure can strand the computer object of your SVM on your Active Directory\.

1. Enter advanced mode in the ONTAP CLI by using the following command\. Replace `FsxId123456789abcdef` with the ID of your Amazon FSx file system\. 

   ```
   FsxId123456789abcdef::> set adv
   ```

   After running this command, you'll see this output\. Enter **y** to continue\. 

   ```
   Warning: These advanced commands are potentially dangerous; use them only when
         directed to do so by NetApp personnel.
         Do you want to continue? {y|n}: y
   ```

1. Delete the DNS for your Active Directory by using the following command\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> vserver services name-service dns dynamic-update record delete -vserver svm_name -lif nfs_smb_management_1
   ```
**Note**  
If the DNS record has already been deleted or if the DNS server is unreachable, this command fails\. If that happens, continue with the next step\.

1. Disable the DNS by using the following command\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> vserver services name-service dns dynamic-update modify -vserver svm_name -is-enabled false -use-secure false
   ```

   If this command is successful, you'll see the following output:

   ```
   Warning: DNS updates for Vserver "svm_name" are now disabled.
        Any LIFs that are subsequently modified or deleted
        can result in a stale DNS entry on the DNS server,
        even when DNS updates are enabled again.
   ```

1. Unjoin the device from Active Directory\. Replace `svm_name` with the name of your SVM\.

   ```
   FsxId123456789abcdef::> vserver cifs delete -vserver svm_name
   ```

   After running this command, you'll see the following output, where `CORP.EXAMPLE.COM` is replaced with the name of your domain\. When prompted, enter your user name and password\. When asked if you want to delete the server, enter **y**\. 

   ```
   In order to delete an Active Directory machine account for the CIFS server,
   you must supply the name and password of a Windows account with sufficient
   privileges to remove computers from the "CORP.EXAMPLE.COM" domain.
   Enter the user name: admin
   Enter the password:
   Warning: There are one or more shares associated with this CIFS server
        Do you really want to delete this CIFS server and all its shares? {y|n}: y
   Warning: Unable to delete the Active Directory computer account for this CIFS server.
        Do you want to continue with CIFS server deletion anyway? {y|n}: y
   ```

## Volume deletion: FlexCache relationship<a name="deletion-flexcache"></a>

You cannot delete volumes that are the origin volumes for a FlexCache relationship unless you delete the cache relationship first\. To determine which volumes have a FlexCache relationship, you can use the ONTAP CLI\. To access the ONTAP CLI, follow the steps in [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. 

1. Check for FlexCache relationships by using the following command\.

   ```
   FsxId123456789abcdef::> volume flexcache origin show-caches
   ```

1. Delete any cache relationships by using the following command\. Replace `dest_svm_name`, and `dest_vol_name` with your actual values\.

   ```
   FsxId123456789abcdef::> volume flexcache delete -vserver dest_svm_name -volume dest_vol_name
   ```

1. After you've deleted the cache relationship, try to delete your SVM through the AWS CLI, API, or console again\.