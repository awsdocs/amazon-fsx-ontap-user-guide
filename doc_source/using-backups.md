# Working with backups<a name="using-backups"></a>

With FSx for ONTAP, you can take automatic daily backups and user\-initiated backups of the volumes on your file system\. Amazon FSx backups are per volume; that is, each backup contains only the data in a particular volume\. Amazon FSx backups are highly durable and incremental\.

Amazon FSx backups are incremental, whether they are generated using the automatic daily backup or the user\-initiated backup feature\. This means that only the data on the volume that has changed after your most recent backup is saved\. This minimizes the time required to create the backup and saves on storage costs by not duplicating data\. When you delete a backup, only the data unique to that backup is removed\. Each Amazon FSx backup contains all of the information that is needed to create a new volume from the backup, effectively restoring a point\-in\-time snapshot of the file system volume\.

Creating regular backups for your volumes is a best practice that helps support your backup retention and compliance needs\. Working with Amazon FSx backups is easy, whether it's creating backups, restoring from a backup, or deleting a backup\.

Amazon FSx supports backing up ONTAP FlexVol volumes with an OntapVolumeType of `RW` \(read/write\)\. In order to back up your data, your volume and your file system's primary SSD storage tier must both also have enough available storage capacity to take a backup snapshot\. For the backup snapshot to be taken, the additional capacity consumed by the volume snapshot cannot cause the volume to exceed 98% utilization\. It is best practice to not exceed 80% utilization of your volume's primary SSD storage tier\.

**Note**  
Amazon FSx does not currently support backing up `DP` \(data\-protection\) volumes, `LS` \(load\-sharing\) volumes, or FlexGroup volumes\.

**Topics**
+ [How backups work](#how-backups-work)
+ [Working with automatic daily backups](#automatic-backups)
+ [Working with user\-initiated backups](#user-initiated-backups)
+ [Using AWS Backup with Amazon FSx](#aws-backup-and-fsx)
+ [Restoring backups](#restoring-backups)
+ [Deleting backups](#delete-backups)

## How backups work<a name="how-backups-work"></a>

Amazon FSx backups use snapshots – point\-in\-time, read\-only images of your volumes – to maintain incrementality between backups\. Each time a backup is taken, Amazon FSx first takes a snapshot of your volume \(the snapshot is stored in your volume, and consumes space on your SSD storage tier\)\. Amazon FSx then compares this snapshot to the previous backup snapshot \(if one exists\) and copies only the changed data into your backup\. If no prior backup snapshot exists, then the entire contents of the most recent backup snapshot is copied into your backup\. After the latest backup snapshot is successfully taken, Amazon FSx deletes the previous backup snapshot\. The snapshot used for the latest backup remains in your volume until the next backup is taken, when the process repeats\. 

## Working with automatic daily backups<a name="automatic-backups"></a>

By default, Amazon FSx takes an automatic daily backup of your file system's volumes\. These automatic daily backups occur during the daily backup window that was established when you created the file system\. At some point during the daily backup window, storage I/O might be suspended briefly while the backup process initializes \(typically for less than a few seconds\)\. When you choose your daily backup window, we recommend that you choose a convenient time of the day\. This time ideally is outside of the normal operating hours for the applications that use your volumes\.

Automatic daily backups are kept for a certain period of time, known as a retention period\. The default retention period for automatic daily backups is 7 days\. You can set the retention period for automatic backups to be between 1–90 days\.

**Note**  
While automatic daily backups have a maximum retention period of 90 days, user\-initiated backups \(including backups taken with AWS Backup\) are kept forever, unless you or the AWS Backup service deletes them\. For more information about user\-initiated backups, see [Working with user\-initiated backups](#user-initiated-backups)\.\.

Automatic daily backups are deleted when the volume is deleted\. If you set the retention period to 0, automatic backups will be disabled\.

Both the daily backup window and retention period are system\-level settings that apply to all the volumes on your file system\. You can use the Amazon FSx console, AWS CLI, or one of the AWS SDKs to change the backup window and backup retention period for your file systems\. Use the [https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) API operation or the [https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command\.

## Working with user\-initiated backups<a name="user-initiated-backups"></a>

With Amazon FSx, you can manually take backups of your file system's volumes at any time\. You can do so using the Amazon FSx console, API, or the AWS Command Line Interface \(AWS CLI\)\. Your user\-initiated backups never expire, and they are available for as long as you want to keep them\. User\-initiated backups are retained even after you delete the volume or the file system the backups were taken on\. You can delete user\-initiated backups only by using the Amazon FSx console, API, or CLI\. They are never automatically deleted by Amazon FSx\. For more information, see [Deleting backups](#delete-backups)\.

### Creating user\-initiated backups<a name="creating-backups"></a>

The following procedure guides you through how to create a user\-initiated backup in the Amazon FSx console for a volume of an existing file system\.

**To create a user\-initiated backup of a volume**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems** and choose the ONTAP file system that you want to back up a volume for\.

1. Choose the **Volumes** tab\.

1. Choose the volume you want to back up\.

1. From **Actions**, choose **Create backup**\.

1. In the **Create backup** dialog box that opens, provide a name for your backup\. Backup names can be a maximum of 256 Unicode characters, including letters, white space, numbers, and the special characters \. \+ \- = \_ : /

1. Choose **Create backup**\.

You have now created a backup of one of your file system's volumes\. You can find a table of all your backups in the Amazon FSx console by choosing **Backups** in the left side navigation\. You can search for the name you gave your backup, and the table filters to only show matching results\.

When you create a user\-initiated backup as this procedure described, it has the type `USER_INITIATED`, and it has the `CREATING` status until it is fully available\.

## Using AWS Backup with Amazon FSx<a name="aws-backup-and-fsx"></a>

AWS Backup is a simple and cost\-effective way to protect your data by backing up your Amazon FSx for NetApp ONTAP volumes\. AWS Backup is a unified backup service designed to simplify the creation, restoration, and deletion of backups, while providing improved reporting and auditing\. AWS Backup makes it easier to develop a centralized backup strategy for legal, regulatory, and professional compliance\. AWS Backup also makes protecting your AWS storage volumes, databases, and file systems simpler by providing a central place where you can do the following:
+ Configure and audit the AWS resources that you want to back up\.
+ Automate backup scheduling\.
+ Set retention policies\.
+ Monitor all recent backup, copy, and restore activity\.

AWS Backup uses the built\-in backup functionality of Amazon FSx\. Backups taken from the AWS Backup console have the same level of file system consistency and performance, are incremental relative to any other Amazon FSx backups you take of your volume \(user\-initiated or automatic\), and offer the same restore options as backups taken through the Amazon FSx console\. If you use AWS Backup to manage these backups, you gain additional functionality, such as the ability to create scheduled backups as frequently as every hour\. In addition, AWS Backup retains your immutable backups even after the source file system is deleted\. This protects against accidental or malicious deletion\.

Backups taken by AWS Backup are considered user\-initiated backups, and they count toward the user\-initiated backup quota for Amazon FSx\. You can view and restore backups taken by AWS Backup in the Amazon FSx console, CLI, and API\. However, you can't delete backups taken by AWS Backup in the Amazon FSx console, CLI, or API\. For more information about how to use AWS Backup to back up your Amazon FSx file systems and how to delete backups, see [Working with Amazon FSx File Systems](https://docs.aws.amazon.com/aws-backup/latest/devguide/working-with-other-services.html#working-with-fsx) in the *AWS Backup Developer Guide*\.

## Restoring backups<a name="restoring-backups"></a>

You can use an available backup to create a new volume, effectively restoring a point\-in\-time snapshot of a volume\. You can restore a backup using the console, AWS CLI, or one of the AWS SDKs\. When you restore a backup, Amazon FSx must download the data in your backup onto the file system before the volume can be brought online\. This process utilizes the unused portion of your file system's throughput capacity, and can take from a few minutes to a few hours depending on the size of your backup and level of unused throughput capacity on your file system\.

FSx for ONTAP initially downloads all the data from the backup onto the volume's SSD tier while the volume is in the RESTORING phase\. This happens regardless of the volume's tiering policy\. Therefore you need to ensure that the volume has enough SSD storage capacity for all the data in the volume that you're restoring\.

The following procedure guides you through how to restore a backup using the console to create a new volume\.

**To restore Amazon FSx for NetApp ONTAP volumes:**

1. Open the AWS Backup console at [https://console\.aws\.amazon\.com/backup](https://console.aws.amazon.com/backup)\.

1. In the navigation pane, choose **Protected resources**, and then choose the Amazon FSx resource ID that you want to restore\.

1. On the **Resource details** page, a list of recovery points for the selected resource ID is shown\. Choose the recovery point ID of the resource\. 

1.  In the upper\-right corner of the pane, choose **Restore** to open the **Restore** page\.

   The first section, **File system details**, displays the recovery point ID, the file system ID, and the file system type\.

1. Under **Restore options**, there are several selections\. First, choose the **File system** from the dropdown menu\.

1. Next, choose the preferred **Storage virtual machine** from the dropdown menu\.

1. Enter a name for your volume\.

1. Specify the **Junction Path**, which is location within your file system where your volume will be mounted\.

1. Specify the **Volume size** in megabytes \(MB\) that you are creating\.

1. \(*Optional*\) You can choose to **Enable storage efficiency** by checking the box\. This will allow deduplication, compression, and compaction\.

1. In the **Capacity pool tiering policy** dropdown menu, select the tiering preference\.

1. In the **Restore permissions**, choose the IAM role that AWS Backup will use to restore backups\.

1. Verify all your entries, and choose **Restore Backup**\.

## Deleting backups<a name="delete-backups"></a>

Deleting a backup is a permanent, unrecoverable action\. Any data in a deleted backup is also deleted\. Do not delete a backup unless you're sure you won't need that backup again in the future\. You can't delete backups taken by AWS Backup, which have type **AWS Backup**, in the Amazon FSx console, CLI, or API\. For information on deleting backups taken with AWS Backup, see [Deleting Backups](https://docs.aws.amazon.com/aws-backup/latest/devguide/deleting-backups.html) in the *AWS Backup Developer Guide*\.

**Important**  
Do not delete the snapshot that is used to maintain incrementality between your backups\. Deleting the backup snapshot will cause the next backup to be of the entire volume rather than only of changed data\.

**To delete a backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose **Backups** from the left side navigation\.

1. Choose the backup that you want to delete from the **Backups** table, and then choose **Delete backup**\.

1. In the **Delete backups** dialog box that opens, confirm that the ID of the backup identifies the backup that you want to delete\.

1. Confirm that the check box is checked for the backup that you want to delete\.

1. Choose **Delete backups**\.

Your backup and all included data are now permanently and irrecoverably deleted\.