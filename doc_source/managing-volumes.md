# Managing FSx for ONTAP volumes<a name="managing-volumes"></a>

Each storage virtual machine \(SVM\) on an FSx for ONTAP file system can contain one or more *volumes*, which are isolated data containers for files, directories, or iSCSI logical units of storage \(LUNs\)\. Volumes are *thin provisioned*, meaning that they consume storage capacity only for the data stored in them\.

You can access a volume from Linux, Windows, or macOS clients over the Network File System \(NFS\) protocol, the Server Message Block \(SMB\) protocol, or over the Internet Small Computer Systems Interface \(iSCSI\) protocol by creating an iSCSI LUN \(shared block storage\)\. FSx for ONTAP also supports multi\-protocol access \(concurrent NFS and SMB access\) to the same volume\.

When you create or update a volume, two important configuration settings are the capacity pool tiering policy and the security style, as explained in the following sections\.

## Volume data\-tiering policy<a name="volume-tiering-policy"></a>

Capacity pool storage is an elastic storage tier that can scale to petabytes in size and is cost\-optimized for colder data\. When you enable tiering on a volume, FSx for ONTAP automatically transitions data between primary storage and capacity pool storage based on your data access patterns\. Tiering allows you to reduce your storage costs and store virtually unlimited data in a cluster\. Capacity pool storage automatically grows and shrinks as you tier data to it, providing elastic storage for the portion of your dataset that grows over time\.

You configure data\-tiering policies on a per\-volume basis\. Amazon FSx supports the following tiering policies:
+ **Auto** moves cold user data blocks in both the active file system and the snapshot copies to the storage pool tier\.

  If data is read from the capacity pool tier in a random fashion, the cold data blocks in the capacity tier become hot and move to the primary storage tier\. If read by sequential reads, such as those associated with index and antivirus scans, the cold data blocks stay cold and do not move to the primary storage tier\.
+ **Snapshot Only** moves user data blocks of the volume snapshot copies that are not associated with the active file system to the storage pool tier\.

  If read, cold data blocks on the capacity tier become hot and are moved to the primary storage tier\.
+ **All** moves cold user data blocks in both the snapshot copies and the active file system to the storage pool tier\.

  If read, cold data blocks on the storage pool tier stay cold and are not written back to the primary storage tier\.
+ **None** \(default\) keeps a volume's data in the primary storage tier, preventing it from being moved to the storage pool tier\.

You can also specify a minimum cooling period for tiering, which sets the time that user data in a volume must remain inactive before the data is considered cold and moved to the storage pool tier\. The minimum cooling period, which applies to `Snapshot Only` and `Auto` tiering policies, ranges from 2–183 days\. \(The default is 2 days for `Snapshot Only` and 31 days for `Auto` policies\.\)

**Note**  
We recommend that you do not exceed 80% storage capacity utilization for a volume to ensure that there is capacity for volume snapshots and backups, and for additional data\. If a volume is consistently above 80% storage capacity utilization, you can [increase the volume size](#updating-volumes) using the Amazon FSx console, CLI, or API\. A volume is considered full when it reaches 98% storage capacity utilization\.

## Volume security style<a name="volume-security-style"></a>

A volume's *security style* setting determines what type of permissions are used for data on volumes when authorizing users\. The two factors that you use to determine the security style for a volume are the type of administrator that manages the file system and the type of user or service that accesses the data on the volume\.

When you configure the security style on a volume, consider the needs of your environment to ensure that you select the best security style in order to avoid issues with managing permissions\. Keep in mind that security style doesn't determine which client types can access data\. Security style determines the permissions that are used to allow data access and the client types that can modify those permissions\. Following are considerations that can help you decide which security style to choose for a volume:
+ **Unix \(Linux\)** – Choose this security style if the file system is managed by a Unix administrator, the majority of users are NFS clients, and an application accessing the data uses a Unix user as the service account\. Only Linux clients can modify permissions with the Unix security style, and the type of permissions used on files and directories are mode\-bits or NFS v4\.x ACLs\. 
+ **NTFS** – Choose this security style if the file system is managed by a Windows administrator, the majority of users are SMB clients, and an application accessing the data uses a Windows user as the service account\. If any Windows access is required to a volume, we recommend that you use the NTFS security style\. Only Windows clients can modify permissions with NTFS security style, and the types of permissions used on file and directories is NTFS ACLs\.
+ **Mixed** – Choose this security style if the file system is managed by both Unix and Windows administrators and users consist of both NFS and SMB clients\. Use the **Mixed** security style when you need to manage multiple types of permissions on the same volume, depending on the type of client accessing data\. An individual file or directory uses only one type of security style at a time, and different directories on a volume will use different access control, with some using UNIX permissions, while others use NTFS permissions\. Both Linux and Windows clients can modify permissions, and both mode\-bits or NFS v4\.x ACLs and NTFS permissions are used\.

Keep in mind that security styles can differ between volumes\.

**Note**  
When creating a volume in the Amazon FSx console, the security style is automatically set to the root volume's security style\. You can't view or change a security style using the console\.   
When creating a volume in the AWS CLI or the API, you can specify the volume's security style\. Otherwise, the security style is set to the root volume's security style by default\. You can view and change a volume's security style in the AWS CLI or the API\.

## Creating a volume<a name="creating-volumes"></a>

You can create an FSx for ONTAP volume using the Amazon FSx console, the AWS CLI, and the Amazon FSx API, in addition to the NetApp ONTAP command line interface \(CLI\) and REST API\.

### To create a volume \(console\)<a name="create-volume-console"></a>

**Note**  
The volume's security style is automatically set to the root volume's security style\.

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, and then choose the ONTAP file system that you want to create a volume for\.

1. Choose the **Volumes** tab\.

1. Choose **Create volume**\.

   The **Create volume** dialog box appears\.  
![\[The Create new volume dialog box.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/create-volume.png)

1. For **File system**, choose the file system to create the volume on\.

1. For **Storage virtual machine**, choose the storage virtual machine \(SVM\) that this volume is created on\.

1. In the **Volume name** field, provide a name for the volume\. You can use a maximum of 203 alphanumeric characters, plus the underscore \(\_\) special character\.

1. For **Junction path**, enter a location within the file system where the volume will be mounted\. The name must have a leading forward slash, for example `/vol3`\.

1. For **Volume size**, enter any whole number in the range of 20–104857600 to specify the size in mebibytes \(MiB\)\.

1. For **Storage efficiency**, choose **Enabled** to enable the ONTAP storage\-efficiency features \(deduplication, compression, and compaction\)\.

1. For **Capacity pool tiering policy**, choose the storage pool tiering policy for the volume, which can be **Auto** \(the default\), **Snapshot Only**, **All**, or **None**\. For more information about capacity pool tiering policies, see [Volume data\-tiering policy](#volume-tiering-policy)\.

1. Choose **Confirm** to create the volume\.

You can monitor the update progress on the **File systems** detail page, in the **Status** column of the **Volumes** pane\. The volume is ready for use when its status is **Created**\.

### To create a volume \(CLI\)<a name="create-volume-cli"></a>
+ To create an FSx for ONTAP volume, use the [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-volume.html) CLI command \(or the equivalent [CreateVolume](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateVolume.html) API operation\), as shown in the following example\.

  ```
  aws fsx create-volume \
      --volume-type ONTAP \
      --name vol1 \
      --ontap-configuration JunctionPath=/vol1,SecurityStyle=NTFS,SizeInMegabytes=1024,StorageVirtualMachineId=svm-abcdef0123456789a
  ```

After successfully creating the volume, Amazon FSx returns its description in JSON format, as shown in the following example\.

```
{
    "Volume": {
        "CreationTime": "2022-08-12T13:03:37.625000-04:00",
        "FileSystemId": "fs-abcdef0123456789c",
        "Lifecycle": "CREATING",
        "Name": "vol1",
        "OntapConfiguration": {
            "FlexCacheEndpointType": "NONE",
            "JunctionPath": "/vol1",
            "SecurityStyle": "NTFS",
            "SizeInMegabytes": 1024,
            "StorageEfficiencyEnabled": true,
            "StorageVirtualMachineId": "svm-abcdef0123456789a",
            "StorageVirtualMachineRoot": false,
            "TieringPolicy": {
                "Name": "NONE"
            },
            "OntapVolumeType": "RW"
        },
        "ResourceARN": "arn:aws:fsx:us-east-2:111122223333:volume/fs-abcdef0123456789c/fsvol-abcdef0123456789b",
        "VolumeId": "fsvol-abcdef0123456789b",
        "VolumeType": "ONTAP"

                        
    }
}
```

## Updating a volume configuration<a name="updating-volumes"></a>

You can update the configuration of an FSx for ONTAP volume using the Amazon FSx console, the AWS CLI, and the Amazon FSx API, in addition to the NetApp ONTAP command line interface \(CLI\) and REST API\.

### To update a volume configuration \(console\)<a name="update-volume-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems** and choose the ONTAP file system that you want to update a volume for\.

1. Choose the **Volumes** tab\.

1. Choose the volume that you want to update\.

1. For **Actions**, choose **Update volume**\.

   The **Update volume** dialog box displays with the volume's current settings\.

1. For **Junction path**, enter an existing location within the file system where the volume will be mounted\. The name must have a leading forward slash, such as `/vol5`\.

1. For **Volume size**, enter any whole number in the range of 20–104867600 to specify the new size in mebibytes \(MiB\)\. You can increase or decrease the size of the volume\.

1. For **Storage efficiency**, choose **Enabled** to enable the ONTAP storage efficiency features \(deduplication, compression, and compaction\), or choose **Disabled** to disable them\.

1. For **Capacity pool tiering policy**, choose a new storage pool tiering policy for the volume, which can be **Auto** \(the default\), **Snapshot\-only**, **All**, or **None**\. For more information about capacity pool tiering policies, see [Volume data\-tiering policy](#volume-tiering-policy)\.

1. Choose **Update** to update the volume\.

### To update a volume configuration \(CLI\)<a name="update-volume-cli"></a>
+ To update the configuration of an FSx for ONTAP volume, use the [update\-volume](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-volume.html) CLI command \(or the equivalent [UpdateVolume](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateVolume.html) API operation\), as shown in the following example\.

  ```
  aws fsx update-volume \
      --volume-id fsvol-1234567890abcde \
      --name new_vol \
      --ontap-configuration JunctionPath=/new_vol,SizeInMegabytes=2048,StorageEfficiencyEnabled=true
  ```

## Deleting a volume<a name="deleting-volumes"></a>

You can delete an FSx for ONTAP volume using the Amazon FSx console, the AWS CLI, and the Amazon FSx API, in addition to the NetApp ONTAP command line interface \(CLI\) and REST API\.

**Important**  
You can only delete volumes using the Amazon FSx console, API, or CLI if the volume has Amazon FSx backups enabled\.

**Important**  
When you delete a volume from the Amazon FSx console, you are given the option to take a final backup of the volume\. New volumes can be created from backups\. We recommend that you choose to take a final backup as a best practice\. If you find you don't need it after a certain period of time, you can delete this and other manually created volume backups\. By default, the `delete-volume` CLI command takes a final backup\.

Before you delete a volume, make sure that no applications are accessing the data in the volume that you want to delete\.

### To delete a volume \(console\)<a name="delete-volume-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, and then choose the ONTAP file system that you want to delete a volume from\.

1. Choose the **Volumes** tab\.

1. Choose the volume that you want to delete\.

1. For **Actions**, choose **Delete volume**\.

1. In the confirmation dialog box, for **Create final backup**, you have two options:
   + Choose **Yes** to take a final backup of the volume\. The name of the final backup is displayed\.
   + Choose **No** if you don't want a final backup of the volume\. You are asked to acknowledge that once the volume is deleted, automatic backups will no longer be available\.

1. Confirm the volume deletion by entering **delete** in the **Confirm delete** field\.

1. Choose **Delete volume\(s\)**\.

### To delete a volume \(CLI\)<a name="delete-volume-cli"></a>
+ To delete an FSx for ONTAP volume, use the [delete\-volume](https://docs.aws.amazon.com/cli/latest/reference/fsx/delete-volume.html) CLI command \(or the equivalent [DeleteVolume](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DeleteVolume.html) API operation\), as shown in the following example\.

  ```
  aws fsx delete-volume --volume-id fsvol-1234567890abcde
  ```

## Viewing a volume<a name="viewing-volumes"></a>

You can see the FSx for ONTAP volumes that are currently on your file system using the Amazon FSx console, the AWS CLI, and the Amazon FSx API and SDKs\.

**To view the volumes on your file system:**
+ **Using the console** – Choose a file system to view the **File systems** detail page\. Choose the **Volumes** tab to list all the volumes on the file system, and then choose the volume you want to view\.
+ **Using the CLI or API** – Use the [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-volumes.html) CLI command or the [DescribeVolumes](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeVolumes.html) API operation\.