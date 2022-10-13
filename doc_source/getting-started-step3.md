# Step 3: Clean up resources<a name="getting-started-step3"></a>

After you have finished this exercise, you should follow these steps to clean up your resources and protect your AWS account\.

**To clean up resources**

1. On the Amazon EC2 console, terminate your instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the Amazon FSx console, delete all of your FSx for ONTAP volumes that are not root volumes of your SVM\. For more information, see [Deleting a volume](managing-volumes.md#deleting-volumes)\.

1. Delete all of your FSx for ONTAP SVMs\. For more information, see [Deleting a storage virtual machine](managing-svms.md#deleting-svms)\.

1. On the Amazon FSx console, delete your file system\. When you delete a file system, all automatic backups are deleted automatically\. However, you still must delete any manually created backups\. The following steps outline this process\.

   1. From the console dashboard, choose the name of the file system that you created for this exercise\.

   1. For **Actions**, choose **Delete file system**\.

   1. In the **Delete file system** dialog box, enter the ID of the file system that you want to delete in the **File system ID** box\.

   1. Choose **Delete file system**\.

   1. The file system is now being deleted, and its status in the dashboard changes to **DELETING**\. When the file system has been deleted, it no longer appears in the dashboard\. Any automatic backups are deleted along with the file system\.

   1. Now you can delete any manually created backups for your file system\. From the left\-side navigation, choose **Backups**\.

   1. From the dashboard, choose any backups that have the same **File system ID** as the file system that you deleted, and choose **Delete backup**\. Be sure to retain the final backup, if you created one\.

   1. The **Delete backups** dialog box opens\. Keep the check box selected for the IDs of the backups that you want to delete, and then choose **Delete backups**\.

   Your Amazon FSx file system and any related automatic backups are now deleted, along with any manual backups that you chose to delete as well\.