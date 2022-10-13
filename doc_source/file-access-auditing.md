# File access auditing<a name="file-access-auditing"></a>

Amazon FSx for NetApp ONTAP supports auditing of end\-user accesses to files and directories in a storage virtual machine \(SVM\)\.

**Topics**
+ [File access auditing overview](#auditing-overview)
+ [Overview of tasks for setting up file access auditing](#auditing-tasks)

## File access auditing overview<a name="auditing-overview"></a>

File access auditing enables you to record end\-user accesses of individual files and directories based on audit policies you define\. File access auditing can help you improve your system's security and reduce the risk of unauthorized access to your system data\. File access auditing helps your organizations remain compliant with data protection requirements, identify potential threats early, and reduce the risk of a data breach\.

Across file and directory accesses, Amazon FSx supports logging of successful attempts \(such as a user with sufficient permissions successfully accessing a file\), failed attempts, or both\. You can also turn off file access auditing at any time\.

By default, audit event logs are stored in the `EVTX` file format, which allows you to view them using Microsoft Event Viewer\.

### SMB access events that can audited<a name="smb-audited-events"></a>

The following table lists the SMB file and folder access events can be audited\.


****  

| Event ID \(EVT/EVTX\) | Event | Description | Category | 
| --- | --- | --- | --- | 
|  560/4656  |  Open Object/Create Object  |  OBJECT ACCESS: Object \(file or directory\) open  |  File Access  | 
|  563/4659  |  Open Object with the Intent to Delete  |  OBJECT ACCESS: A handle to an object \(file or directory\) was requested with the Intent to Delete  |  File Access  | 
|  564/4660  |  Delete Object  |  OBJECT ACCESS: Delete Object \(file or directory\)\. ONTAP generates this event when a Windows client attempts to delete the object \(file or directory\)  |  File Access  | 
|  567/4663  |  Read Object/Write Object/Get Object Attributes/Set Object Attributes  |  OBJECT ACCESS: Object access attempt \(read, write, get attribute, set attribute\)\. For this event, ONTAP audits only the first SMB read and first SMB write operation \(success or failure\) on an object\. This prevents ONTAP from creating excessive log entries when a single client opens an object and performs many successive read or write operations to the same object\.  |  File Access  | 
|  N/A/4664  |  Hard link  |  OBJECT ACCESS: An attempt was made to create a hard link  |  File Access  | 
|  N/A/N/A ONTAP Event ID 9999  |  Rename Object  |  OBJECT ACCESS: Object renamed\. This is an ONTAP event\. It is not currently supported by Windows as a single event\.  |  File Access  | 
|  N/A/N/A ONTAP Event ID 9998  |  Unlink Object  |  OBJECT ACCESS: Object unlinked\. This is an ONTAP event\. It is not currently supported by Windows as a single event\.  |  File Access  | 

### NFS access events that can audited<a name="nfs-audited-events"></a>

The following NFS file and folder access events can be audited\.
+ READ
+ OPEN
+ CLOSE
+ READDIR
+ WRITE
+ SETATTR
+ CREATE
+ LINK
+ OPENATTR
+ REMOVE
+ GETATTR
+ VERIFY
+ NVERIFY
+ RENAME

## Overview of tasks for setting up file access auditing<a name="auditing-tasks"></a>

Setting up FSx for ONTAP for file access auditing involves the following high\-level tasks:

1. [Familiarize yourself](#auditing-requirements) with the file access auditing requirements and considerations\.

1. [Create an auditing configuration](#create-audit-config) on a specific SVM\.

1. [Enable auditing](#enable-auditing) on that SVM\.

1. [Configure audit policies](#file-audit-policies) on your files and directories\.

1. [View the audit event logs](#view-audit-logs) after FSx for ONTAP emits them\.

Task details are provided in the following procedures\.

Repeat the tasks for any other SVM on your file system that you want to enable file access auditing for\.

### Auditing requirements<a name="auditing-requirements"></a>

Before you configure and enable auditing on an SVM, you should be aware of the following requirements and considerations\.
+ NFS auditing supports audit Access Control Entries \(ACEs\) designated as type `u`, which generate an audit log entry when access is attempted on the object\. For NFS auditing, there is no mapping between mode bits and audit ACEs\. When converting ACLs to mode bits, audit ACEs are skipped\. When converting mode bits to ACLs, audit ACEs are not generated\.
+ Auditing is dependent on having available space in the staging volumes\. \(A staging volume is dedicated volume created by ONTAP to store staging files, which are intermediate binary files on individual nodes where audit records are stored prior to conversion to an EVTX or XML file format\.\) You must ensure that there is sufficient space for the staging volumes in aggregates that contain audited volumes\.
+ Auditing is dependent on having available space in the volume containing the directory where converted audit event logs are stored\. You must ensure that there is sufficient space in the volumes used to store event logs\. You can specify the number of audit logs to retain in the auditing directory by using the `-rotate-limit` parameter when creating an auditing configuration, which can help to ensure that there is enough available space for the audit logs in the volume\.

### Creating auditing configurations on SVMs<a name="create-audit-config"></a>

Before you can begin auditing file and directory events, you must create an auditing configuration on the Storage Virtual Machine \(SVM\)\. After you create the auditing configuration, you must enable it on the SVM\.

Before you use the `vserver audit create` to create the auditing configuration, make sure you've created a directory that will be used as the destination for logs, and that the directory doesn't have symlinks\. You specify the destination directory with the `-destination` parameter\.

You can create an auditing configuration that rotates audit logs based on log size or a schedule, as follows:
+ To rotate audit logs based on log size, use this command:

  ```
  vserver audit create -vserver svm_name -destination path [-format {xml|evtx}] [-rotate-limit integer] [-rotate-size {integer[KB|MB|GB|TB|PB]}]
  ```

  The following example creates an auditing configuration for the SVM named `svm1` that audits file operations and CIFS \(SMB\) logon and logoff events \(the default\) using size\-based rotation\. The log format is `EVTX` \(the default\), logs are stored in the `/audit_log` directory, and you'll have a single log file at a time \(up to 200MB in size\)\.

  ```
  vserver audit create -vserver svm1 -destination /audit_log -rotate-size 200MB
  ```
+ To rotate audit logs based on a schedule, use this command:

  ```
  vserver audit create -vserver svm_name -destination path [-format {xml|evtx}]
          [-rotate-limit integer] [-rotate-schedule-month chron_month]
          [-rotate-schedule-dayofweek chron_dayofweek] [-rotate-schedule-day chron_dayofmonth]
          [-rotate-schedule-hour chron_hour] [-rotate-schedule-minute chron_minute]
  ```

  The `-rotate-schedule-minute` parameter is required if you are configuring time\-based audit log rotation\.

  The following example creates an auditing configuration for the SVM named `svm2` using time\-based rotation\. The log format is `EVTX` \(the default\) and the audit logs are rotated monthly, at 12:30 PM on all days of the week\.

  ```
  vserver audit create -vserver svm2 -destination /audit_log -rotate-size 200MB  -rotate-schedule-month all -rotate-schedule-dayofweek all -rotate-schedule-hour 12 -rotate-schedule-minute 30
  ```

You can use the `-format` parameter to specify whether the audit logs are created in the converted `EVTX` format \(the default\) or in the `XML` file format\. The `EVTX` format allows you to view the log files with Microsoft Event Viewer\.

By default, the categories of events to be audited are file access events \(both SMB and NFS\), CIFS \(SMB\) logon and logoff events, and authorization policy change events\. You can have greater control over which events to log by the `-events` parameter, which has the following format:

```
-events {file-ops|cifs-logon-logoff|cap-staging|file-share|audit-policy-change|user-account|authorization-policy-change|security-group}
```

For example, using `-events file-share` enables auditing of file share events\.

For more information on the `vserver audit create` command, see [ Create an audit configuration](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__audit__create.html)\.

### Enabling auditing on an SVM<a name="enable-auditing"></a>

After you finish setting up the auditing configuration, you must enable auditing on the SVM\. To do so, use the following command:

```
vserver audit enable -vserver svm_name
```

For example, use the following command to enable auditing on the SVM named `svm1`\.

```
vserver audit enable -vserver svm1
```

You can disable access auditing at any time\. For example, use the following command to turn off auditing on the SVM named `svm4`\.

```
vserver audit disable -vserver svm4
```

When you disable auditing, the audit configuration isn't deleted on the SVM, which means that you can re\-enable auditing on that SVM at any time\.

### Configuring file and folder audit policies<a name="file-audit-policies"></a>

You need to configure audit policies on the files and folders that you want audited for user access attempts\. You can configure audit policies to monitor both successful and failed access attempts\.

You can configure both SMB and NFS audit polices\. SMB and NFS audit policies have different configuration requirements and audit capabilities based on the security style of the volume\.

#### Audit policies on NTFS security\-style files and directories<a name="audit-policies-ntfs"></a>

You can configure NTFS audit policies by using the Windows Security tab or the ONTAP CLI\.

##### To configure NTFS audit policies \(Windows Security tab\)<a name="set-ntfs-audit-policy-windows"></a>

You configure NTFS audit policies by adding entries to NTFS SACLs that are associated with an NTFS security descriptor\. The security descriptor is then applied to NTFS files and directories\. These tasks are automatically handled by the Windows GUI\. The security descriptor can contain discretionary access control lists \(DACLs\) for applying file and folder access permissions, SACLs for file and folder auditing, or both SACLs and DACLs\.

1. From the **Tools** menu in Windows Explorer, select **Map network drive**\.

1. Complete the **Map Network Drive** box:

   1. Choose a **Drive** letter\.

   1. In the **Folder** box, type the SMB \(CIFS\) server name that contains the share, holding the data you want to audit and the name of the share\.

   1. Choose **Finish**\.

   The drive you selected is mounted and ready with the Windows Explorer window displaying files and folders contained within the share\.

1. Select the file or directory for which you want to enable auditing access\.

1. Right\-click the file or directory, and then choose **Properties**\.

1. Choose the **Security** tab\.

1. Click **Advanced**\.

1. Choose the **Auditing** tab\.

1. Perform the desired actions:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/file-access-auditing.html)

   If you are setting up auditing on a user or group or changing auditing on an existing user or group, the **Auditing Entry for *object*** box opens\.

1. In the **Apply to** box, select how you want to apply this auditing entry\.

   If you are setting up auditing on a single file, the **Apply to** box is not active, as it defaults to This object only\.

1. In the **Access** box, select what you want audited and whether you want to audit successful events, failure events, or both\.
   + To audit successful events, choose the **Success** box\.
   + To audit failure events, choose the **Failure** box\.

   Choose the actions that you need to monitor to meet your security requirements\. For more information about these auditable events, see your Windows documentation\. You can audit the following events:
   + Full control
   + Traverse folder / execute file
   + List folder / read data
   + Read attributes
   + Read extended attributes
   + Create files / write data
   + Create folders / append data
   + Write attributes
   + Write extended attributes
   + Delete subfolders and files
   + Delete
   + Read permissions
   + Change permissions
   + Take ownership

1. If you do not want the auditing setting to propagate to subsequent files and folders of the original container, choose the **Apply these auditing entries to objects and/or containers within this container only** box\.

1. Choose **Apply**\.

1. After you finish adding, removing, or editing auditing entries, choose **OK**\.

   The **Auditing Entry for *object*** box closes\.

1. In the **Auditing** box, choose the inheritance settings for this folder\. Choose only the minimal level that provides the auditing events that meet your security requirements\.

   You can choose one of the following:
   + Choose the **Include inheritable auditing entries from this object's parent** box\.
   + Choose the **Replace all existing inheritable auditing entries on all descendants with inheritable auditing entries from this object** box\.
   + Choose both boxes\.
   + Choose neither box\.

   If you are setting SACLs on a single file, the **Replace all existing inheritable auditing entries on all descendants with inheritable auditing entries from this object** box is not present in the **Auditing** box\.

1. Choose **OK**\.

##### To configure NTFS audit policies \(ONTAP CLI\)<a name="set-ntfs-audit-policy-cli"></a>

By using the ONTAP CLI, you can configure NTFS audit policies without needing to connect to the data using an SMB share on a Windows client\.
+ You can configure NTFS audit policies by using the [ vserver security file\-directory](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/TOC__vserver__security__file-directory.html) command family\.

For example, the following command applies a security policy named `p1` to the SVM named `vs0`\.

```
vserver security file-directory apply -vserver vs0 -policy-name p1
```

#### Audit policies on UNIX security\-style files and directories<a name="audit-policies-unix"></a>

You configure auditing for UNIX security\-style files and directories by adding audit ACEs \(access control expressions\) to NFS v4\.x ACLs \(access control lists\)\. This allows you to monitor certain NFS file and directory access events for security purposes\.

**Note**  
For NFS v4\.x, both discretionary and system ACEs are stored in the same ACL\. Therefore, you must be careful when adding audit ACEs to an existing ACL to avoid overwriting and losing an existing ACL\. The order in which you add the audit ACEs to an existing ACL does not matter\.

##### To configure UNIX audit policies<a name="set-unix-audit-policy"></a>

1. Retrieve the existing ACL for the file or directory by using the `nfs4_getfacl` or equivalent command\.

1. Append the desired audit ACEs\.

1. Apply the updated ACL to the file or directory by using the `nfs4_setfacl` or equivalent command\.

   This example uses the `-a` option to give a user \(named `testuser`\) read permissions to the file named `file1`\.

   ```
   nfs4_setfacl -a "A::testuser@example.com:R" file1
   ```

### Viewing audit event logs<a name="view-audit-logs"></a>

You can view audit event logs saved in the `EVTX` or `XML` file formats\.
+ `EVTX` file format – You can open the converted `EVTX` audit event logs as saved files using Microsoft Event Viewer\.

  There are two options that you can use when viewing event logs using Event Viewer:
  + **General view**: Information that is common to all events is displayed for the event record\. The event\-specific data for the event record is not displayed\. You can use the detailed view to display event\-specific data\.
  + **Detailed view**: A friendly view and a XML view are available\. The friendly view and the XML view display both the information that is common to all events and the event\-specific data for the event record\.
+ `XML` file format – You can view and process XML audit event logs on third\-party applications that support the XML file format\. XML viewing tools can be used to view the audit logs provided you have the XML schema and information about definitions for the XML fields\.