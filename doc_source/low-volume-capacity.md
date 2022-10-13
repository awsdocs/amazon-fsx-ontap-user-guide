# You have insufficient volume capacity<a name="low-volume-capacity"></a>

If you are running out of space on your volumes, you use the procedures shown here to diagnose and resolve the situation\.

**Topics**
+ [Determine how a volume's capacity is being used](#determine-volume-capacity-usage)
+ [Increasing the size of a volume](#increase-volume-size)
+ [Enabling volume autosizing](#enable-volume-autosizing)
+ [Increasing the maximum number of files on a volume](#increase-volume-max-files)
+ [Deleting snapshots](#delete-snapshots)
+ [Disabling automatic snapshots](#disable-snapshots)

## Determine how a volume's capacity is being used<a name="determine-volume-capacity-usage"></a>

You can see how your volume's capacity is being used with the [volume show\-space](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__show-space.html) NetApp ONTAP CLI command\.

1. To access the NetApp ONTAP CLI, establish an SSH session on the management port of the Amazon FSx for NetApp ONTAP file system by running the following command\. Replace `management_endpoint_ip` with the IP address of your management port\.

   ```
   [~]$ ssh fsxadmin@management_endpoint_ip
   ```

   For more information, see [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. 

1. Check the space of the volume by running the following command\. Replace `vol_name` with the name of your volume\.

   ```
   ::> volume show-space -volume vol_name
   ```

   If the command is successful, you'll see output similar to the following:

   ```
   Vserver : svm_name
   Volume  : vol_name
   Feature                                    Used      Used%
   --------------------------------     ----------     ------
   User Data                                 140KB         0%
   Filesystem Metadata                     164.4MB         1%
   Inodes                                  10.28MB         0%
   Snapshot Reserve                        563.2MB         5%
   Deduplication                              12KB         0%
   Snapshot Spill                           9.31GB        85%
   Performance Metadata                      668KB         0%
   
   Total Used                              10.03GB        91%
   
   Total Physical Used                     10.03GB        91%
   ```

   The output of this command shows the amount of physical space that different types of data occupy on this volume\. It also shows the percentage of the total volume's capacity that each type of data consumes\. In this example, `Snapshot Spill` and `Snapshot Reserve` consume a combined 90 percent of the volume's capacity\.

To increase the amount of available space, you can either [increase the size](#increase-volume-size) of the volume, or you can [delete snapshots](#delete-snapshots) that you are not using, as shown in the following procedures\. 

For FlexVol volume types \(the default volume type for FSx for ONTAP volumes\), you can also enable [volume autosizing](#enable-volume-autosizing)\. When you enable autosizing, the volume size will automatically increase when it reaches certain thresholds\. You can also disable automatic snapshots\. Both of these features are explained in the following sections\.

## Increasing the size of a volume<a name="increase-volume-size"></a>

You can increase the size of a volume by using the Amazon FSx console, AWS CLI, and Amazon FSx API\. For more information about updating a volume with an increased capacity, see [Updating a volume configuration](managing-volumes.md#updating-volumes)\.

Alternatively, you can set a new size for the volume by using the [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__modify.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__modify.html) NetApp ONTAP CLI command\.

**To increase the size of an FSx for ONTAP volume**

1. To access the NetApp ONTAP CLI, establish an SSH session on the management port of the Amazon FSx for NetApp ONTAP file system by running the following command\. Replace `management_endpoint_ip` with the IP address of your management port\.

   ```
   [~]$ ssh fsxadmin@management_endpoint_ip
   ```

   For more information, see [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. 

1. Run the following command, replacing the following values:
   + Replace *`svm_name`* with the name of the storage virtual machine \(SVM\) that the volume is created on\.
   + Replace *`vol_name`* with name of the volume that you want to resize\.
   + Replace *`vol_size`* with the new size of the volume in the format *`integer`*`[KB|MB|GB|TB|PB]`; for example, `100GB` to increase the volume size to 100 gigabytes\.

   ```
   ::> volume modify -vserver svm_name -volume vol_name -size vol_size
   ```

## Enabling volume autosizing<a name="enable-volume-autosizing"></a>

You can enable volume autosizing so that the volume will automatically grow to a specified size when it reaches a used space threshold\. You can do this for FlexVol volume types \(the default volume type for FSx for ONTAP\) using the [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__autosize.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__autosize.html) NetApp ONTAP CLI command\.

**To enable volume autosizing**

1. To access the NetApp ONTAP CLI, establish an SSH session on the management port of the Amazon FSx for NetApp ONTAP file system by running the following command\. Replace `management_endpoint_ip` with the IP address of your management port\.

   ```
   [~]$ ssh fsxadmin@management_endpoint_ip
   ```

   For more information, see [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. 

1. Use the `volume autosize` command as shown, replacing the following values:
   + Replace *`svm_name`* with the name of the SVM that the volume is created on\.
   + Replace *`vol_name`* with name of the volume that you want to resize\.
   + Replace *`grow_threshold`* with a used space percentage value \(such as `90`\) at which the volume will automatically increase in size \(up to the *`max_size`* value\)\.
   + Replace *`max_size`* with the maximum size that the volume can grow to\. Use the format *`integer`*`[KB|MB|GB|TB|PB]`; for example, `100TB`\. The maximum size is 100 TB\. The default value for *`max_size`* is 120 percent of the size of the volume at the time when autosizing was enabled\.

   ```
   ::> volume autosize -vserver svm_name -volume vol_name -mode grow -grow-threshold-percent grow_threshold -maximum-size max_size
   ```

## Increasing the maximum number of files on a volume<a name="increase-volume-max-files"></a>

FSx for ONTAP volumes can run out of file capacity when the number of available inodes, or file pointers, is exhausted\. By default, the number of available inodes on a volume is 1 for every 32KiB of volume size\. Using this default, a 1 GiB volume can have a maximum of 32,768 inodes, or files, as shown in the following example\.

Volume\_size\_in\_bytes × \(1 file ÷ inode\_size\_in\_bytes\) = maximum\_number\_of\_files

1,073,741,824 bytes × \(1 file ÷ 32,768 bytes\) = 32,768 files

The number of inodes in a volume increases commensurately with the volume's storage capacity, up to a threshold of 648 GiB\. By default, volumes that have storage capacity of 648 GiB or more all have the same number of inodes, 21,251,126\. If you create a volume larger than 648 GiB, and you want to have more that 21,251,126 inodes, you will have to increase the maximum number of files on the volume manually\. For more information, see [To increase the maximum number of files on a volume](#increase-max-files)\.

You can increase the maximum number of inodes that a volume can contain, up to a maximum of 1 inode for every 4KiB of space \(1 inode for every 4KiB data block\)\. Using this setting on the same 1 GiB volume increases the maximum number of files from 32,768 to 262,144, shown as follows\.

1,073,741,824 bytes × \(1 file ÷ 4096 bytes\) = 262,144 files

An FSx for ONTAP volume can have a maximum of 2 billion files\.

More inodes are required for a file of the same size after increasing a volume's maximum file capacity than were required before the increase\. For example, with the default setting of 1 inode per 32 KiB storage, a 32 KiB file requires one inode\. If you increase the maximum file capacity setting to 1 inode per 4 KiB of storage, that 32 KiB file now requires 8 inodes\.

\(1 inode ÷ 4 KiB\) × 32 KiB = 8 inodes

### Volume storage and inodes<a name="inodes-disk-space"></a>

An inode consumes 288 bytes of volume storage\. The default maximum file capacity setting of 1 inode per 32 KiB of storage limits the total amount of storage consumed by inodes to 1% of volume used capacity\. The following example shows the amount of storage consumed by inodes on a 1 GiB volume with the default setting\.

\(1 GiB ÷ 32 KiB\) × 288 bytes = 9 MiB

For a volume using the maximum file capacity setting of 1 inode per 4 KiB, the amount of storage consumed by inodes is approximately 7% of the volume's capacity, shown as follows:

\(1 GiB ÷ 4KiB\) × 288 bytes = 72 MiB

Unused inodes do not consume any space\.

**Note**  
Once you increase a volume's maximum file capacity, you cannot lower it at a later time\.

### Viewing a volume's file capacity<a name="view-volume-file-capacity"></a>

You can use either of the following methods to view the maximum number of files allowed and the number of files already used on a volume\.
+ The CloudWatch volume metrics `FilesCapacity` and `FilesUsed`\.
+ In the Amazon FSx console, navigate to the **Available files \(inodes\)** chart in your volume's **Monitoring** tab\. The following image shows the **Available files \(inodes\)** on a volume decreasing over time\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-available-files.png)

  <a name="increase-max-files"></a>

**To increase the maximum number of files on a volume**

You use the `volume modify` NetApp ONTAP CLI command to increase the maximum number of files on a volume\. For more information, see [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__modify.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/volume__modify.html) in the NetApp ONTAP Documentation Center\.
**Note**  
Once you increase a volume's maximum file capacity, you cannot lower it at a later time\. Increasing the maximum file capacity increases the amount of inodes required for a file of the same size\.

1. To access the NetApp ONTAP CLI, establish an SSH session on the management port of the Amazon FSx for NetApp ONTAP file system by running the following command\. Replace `management_endpoint_ip` with the IP address of your management port\.

   ```
   [~]$ ssh fsxadmin@management_endpoint_ip
   ```

   For more information, see [Managing file systems with the NetApp ONTAP CLI](managing-resources-ontap-apps.md#fsxadmin-ontap-cli)\. 

1. Do one of the following, depending on your use case\. Replace *`svm_name`* and *`vol_name`* with your values\.
   + To configure a volume to always have the maximum number of files \(inodes\) available, perform the following:

     1. Enter advanced mode in the NetApp ONTAP CLI by using the following command\.

        ```
        ::> set adv
        ```

     1. After running this command, you'll see this output\. Enter `y` to continue\.

        ```
        Warning: These advanced commands are potentially dangerous; use them only when
        directed to do so by NetApp personnel.
        Do you want to continue? {y|n}: y
        ```

     1. Enter the following command to always use the maximum number of files on the volume:

        ```
        ::> volume modify -vserver svm_name -volume vol_name -files-set-maximum true
        ```
   + To manually specify the total number of files permitted on the volume, with `max_number_files = (current_size_of_volume) × (1 file ÷ 4 KiB)`, up to a maximum possible value of 2 billion, use the following command:

     ```
     ::> volume modify -vserver svm_name -volume vol_name -files max_number_files
     ```

## Deleting snapshots<a name="delete-snapshots"></a>

[Snapshots](snapshots-ontap.md) \(point\-in\-time read\-only images of a prior state of your volume\) are enabled by default on all FSx for ONTAP volumes to protect your data\. Snapshots consume storage capacity only for the portions of ﬁles that have changed since the last snapshot\. For this reason, if your workload changes data rapidly, snapshots from old data can take up a significant portion of the capacity of your volume\. 

For example, the `volume show-space` command output provided earlier shows 140 KB of `User Data`\. However, the volume had 9\.8 GB of `User Data` before the user data was deleted\. Even if you've deleted the files from your volume, a snapshot might still reference old user data\. Because of this, `Snapshot Reserve` and `Snapshot Spill` in the prior example take up a total of 9\.8 GB of space, even though there is virtually no user data on the volume\.

To free up space on volumes, you can delete older snapshots that you no longer need\. You can do this by creating a snapshot auto\-delete policy or by manually deleting snapshots\. Deleting a snapshot deletes the changed data stored on the snapshot\.

### Manually deleting snapshots<a name="manually-delete-snapshots"></a>

Use the following command to manually delete snapshots\. Before running this command, replace the following values:
+ Replace *`svm_name`* with the name of the SVM that the volume is created on\.
+ Replace *`vol_name`* with name of the volume\.
+ Replace *`snapshot_name`* with the name of the snapshot\. This command supports wildcard characters \(`*`\) for *`snapshot_name`*\. Therefore, you can delete all hourly snapshots, for example, by using `hourly*`\.

**Important**  
If you have Amazon FSx backups enabled, Amazon FSx retains a snapshot for the most recent Amazon FSx backup of each volume\. Those snapshots are used to maintain incrementality between backups, and must not be deleted by using this method\.

```
FsxIdabcdef01234567892::> volume snapshot delete -vserver svm_name -volume vol_name -snapshot snapshot_name
```

For more information about manually deleting snapshots, see the [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-930/volume__snapshot__delete.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-930/volume__snapshot__delete.html) command in the *NetApp ONTAP Documentation Center*\.

### Snapshot autodelete policy<a name="snapshot-autodelete-policy"></a>

You can create a policy to automatically delete snapshots when the amount of available space in your volume is running low\. To establish an autodelete policy for a volume, use the following command\.

Before running this command, replace the following values:
+ Replace *`svm_name`* with the name of the SVM that the volume is created on\.
+ Replace *`vol_name`* with name of the volume\.

For `-trigger`, assign one of the following values:
+ `volume` – Use `volume` if you want the threshold at which snapshots are deleted to correspond to a total used\-volume capacity threshold\. The used\-volume capacity thresholds that trigger snapshot deletion are determined by the size of your volume, with the threshold scaling from 85–98 percent used capacity\. Smaller volumes have a smaller threshold, and larger volumes have a larger one\.
+ `snap_reserve` – Use `snap_reserve` if you want snapshots to be deleted based on what can be held in your snapshot reserve\.

```
::> volume snapshot autodelete modify -vserver svm_name -volume vol_name -enabled true -trigger [volume|snap_reserve]
```

For more information, see the [ volume snapshot autodelete modify](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-cmpr-910%2Fvolume__snapshot__autodelete__modify.html) command in the *NetApp ONTAP Documentation Center*\.

## Disabling automatic snapshots<a name="disable-snapshots"></a>

Snapshots are automatically created using the default snapshot policy for volumes in your FSx for ONTAP file system\. If you don't need snapshots of your data \(for example, if you're using test data\), you can disable snapshots\.

To disable automatic snapshots for a volume, modify the snapshot policy of a volume to use the `none` policy\. The `none` policy has no snapshot schedules defined, so no snapshots will be taken for the volume\.

1. To show the `none` policy, use the following command\.

   ```
   ::> snapshot policy show -policy none
   
   Vserver: FsxIdabcdef01234567892
                            Number of Is
   Policy Name              Schedules Enabled Comment
   ------------------------ --------- ------- ----------------------------------
   none                             0 false   Policy for no automatic snapshots.
       Schedule               Count     Prefix                 SnapMirror Label
       ---------------------- -----     ---------------------- -------------------
       -                          -     -                      -
   ```

1. To disable automatic snapshots, add the `none` policy to your volume by using the following command\.
   + Replace *`svm_name`* with your SVM's name\.
   + Replace *`vol_name`* with your volume's name\.

   When prompted to continue, enter **y**\. 

   ```
   ::> volume modify -vserver svm_name -volume vol_name -snapshot-policy none
   
   Warning: You are changing the Snapshot policy on volume "vol_name" to "none". Snapshot copies on this volume
            that do not match any of the prefixes of the new Snapshot policy will not be deleted. However, when
            the new Snapshot policy takes effect, depending on the new retention count, any existing Snapshot copies
            that continue to use the same prefixes might be deleted. See the 'volume modify' man page for more information. 
   Do you want to continue? {y|n}: y
   Volume modify successful on volume vol_name of Vserver svm_name.
   ```