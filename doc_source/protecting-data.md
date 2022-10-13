# Protecting your data<a name="protecting-data"></a>

Beyond automatically replicating your file system's data to ensure high durability, Amazon FSx provides you with the following options to further protect the data stored on your file systems: 
+ Native Amazon FSx backups support your backup retention and compliance needs within Amazon FSx\. You can also use AWS Backup to centrally manage, automate, and protect your backups across AWS services in the cloud\.
+ Snapshots enable your users to easily undo file changes and compare file versions by restoring files to previous versions\. 
+ Replication of your Amazon FSx file system to a second file system to provide data protection and recovery\. Replication, when enabled, occurs on an automatic, scheduled basis\. 

**Topics**
+ [Working with backups](using-backups.md)
+ [Working with snapshots](snapshots-ontap.md)
+ [Scheduled replication using NetApp SnapMirror](scheduled-replication.md)