# Migrating to FSx for ONTAP using AWS DataSync<a name="migrate-files-to-fsx-datasync"></a>

We recommend using AWS DataSync to transfer data between FSx for ONTAP file systems and non\-ONTAP file systems, including FSx for Lustre, FSx for OpenZFS, FSx for Windows File Server, Amazon EFS, Amazon S3, and on\-premises filers\. If you're transferring files between FSx for ONTAP and NetApp ONTAP, we recommend using [NetApp SnapMirror](migrating-fsx-ontap-snapmirror.md)\. AWS DataSync is a data transfer service that simplifies, automates, and accelerates moving and replicating data between self\-managed storage systems and AWS storage services over the internet or AWS Direct Connect\. DataSync can transfer your file system data and metadata, such as ownership, timestamps, and access permissions\.

You can use DataSync to transfer files between two FSx for ONTAP file systems, and also move data to a file system in a different AWS Region or AWS account\. You can also use DataSync with FSx for ONTAP file systems for other tasks\. For example, you can perform one\-time data migrations, periodically ingest data for distributed workloads, and schedule replication for data protection and recovery\.

In DataSync, a *location* is an endpoint for an FSx for ONTAP file system\. For information about specific transfer scenarios, see [Working with locations](https://docs.aws.amazon.com/datasync/latest/userguide/working-with-locations.html) in the *AWS DataSync User Guide*\.

## Prerequisites<a name="migrate-data-sync-prereq"></a>

To migrate data into your FSx for ONTAP setup, you need a server and network that meet the DataSync requirements\. To learn more, see [Requirements for DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/requirements.html) in the *AWS DataSync User Guide*\.

## Basic steps for migrating files using DataSync<a name="migrate-data-sync-basic-steps"></a>

Transferring files from a source to a destination using DataSync involves the following basic steps:
+ Download and deploy an agent in your environment and activate it \(not required if transferring between AWS services\)\.
+ Create a source and destination location\.
+ Create a task\.
+ Run the task to transfer files from the source to the destination\.

For more information, see the following topics in the *AWS DataSync User Guide*:
+ [ Data transfer between self\-managed storage and AWS](https://docs.aws.amazon.com/datasync/latest/userguide/how-datasync-works.html#onprem-aws)
+ [ Creating a location for Amazon FSx for NetApp ONTAP](https://docs.aws.amazon.com/datasync/latest/userguide/create-ontap-location.html)