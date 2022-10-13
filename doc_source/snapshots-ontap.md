# Working with snapshots<a name="snapshots-ontap"></a>

A *snapshot* is a read\-only image of an Amazon FSx for NetApp ONTAP volume at a point in time\. Snapshots offer protection against accidental deletion or modification of files in your volumes\. With snapshots, your users can easily view and restore individual files or folders from an earlier snapshot\. Doing this enables users to easily undo changes and compare file versions\.

Because snapshots are stored alongside your ﬁle system's data, they consume the ﬁle system's storage capacity\. However, snapshots consume storage capacity only for the changed portions of ﬁles since the last snapshot\. Snapshots stored in your ﬁle system are not included in backups of your ﬁle system volumes\.

Snapshots are enabled by default on your volumes, using the default snapshot policy\. Snapshots are stored in the `.snapshot` directory at the root of a volume\.

**Topics**
+ [Snapshot policies](#snapshot-policies)
+ [Restoring individual files and folders](#snapshots-user-restore)

## Snapshot policies<a name="snapshot-policies"></a>

By default, every volume is associated with the file system's `default` snapshot policy\. The snapshot policy defines how the system creates snapshots for a volume\. The policy specifies when to create snapshots, how many copies to retain, and how to name them\.

The `default` policy automatically creates snapshots on the following schedule, with the oldest snapshot copies deleted to make room for newer copies:
+ A maximum of six hourly snapshots taken five minutes past the hour\.
+ A maximum of two daily snapshots taken Monday through Saturday at 10 minutes after midnight\.
+ A maximum of two weekly snapshots taken every Sunday at 15 minutes after midnight\.

You can store up to 1023 snapshots per volume at any point in time\. When you reach this limit, the next snapshot copy replaces the oldest snapshot copy\.

## Restoring individual files and folders<a name="snapshots-user-restore"></a>

Using the snapshots on your Amazon FSx file system, your users can quickly restore previous versions of individual files or folders\. Doing this enables them to recover deleted or changed files stored on the shared file system\. They do this in a self\-service manner directly on their desktop without administrator assistance\. This self\-service approach increases productivity and reduces administrative workload\.

Linux and macOS clients can view snapshots in the `.snapshot` directory at the root of a volume\. Windows clients can view snapshots in the `Previous Versions` tab of Windows Explorer \(when right\-clicking on a file or folder\)\.

### To restore a file from a snapshot \(Linux and macOS clients\)<a name="user-restore-all-clients"></a>

1. If the original file still exists and you do not want it overwritten by the file in a snapshot, then use your Linux or macOS client to rename the original file or move it to a different directory\.

1. In the `.snapshot` directory, locate the snapshot that contains the version of the file that you want to restore\.

1. Copy the file from the `.snapshot` directory to the directory in which the file originally existed\.

### To restore a file from a snapshot \(Windows clients\)<a name="user-restore-win-clients"></a>

Users on Windows clients can restore files to previous versions using the familiar Windows File Explorer interface\.

1. To restore a file, users choose the file to restore, then choose **Restore previous versions** from the context \(right\-click\) menu\.

1. Users can then view and restore a previous version from the **Previous Versions** list\.

Data in snapshots is read\-only\. If you want to make modifications to files and folders listed in the the **Previous Versions** tab, you must save a copy of the files and folders that you want to modify to a writable location and make modifications to the copies\.