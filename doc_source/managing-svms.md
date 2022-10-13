# Managing FSx for ONTAP storage virtual machines<a name="managing-svms"></a>

You can create one or multiple *storage virtual machines* \(SVMs\) on each FSx for ONTAP file system\. Each SVM is a virtual, isolated file server with its own administrative credentials and IP address for accessing data\.

Every storage virtual machine has a *root volume* \(`/`\) that resides at the top level of the namespace hierarchy and contains junction paths \(also known as mount points\) for the volumes that you create in your SVM\. We recommend that you not store user data in the root volume, but you can create additional volumes within your storage virtual machine at any time\.

You can optionally join your storage virtual machines to your organization's Active Directory during the SVM's creation\. Joining an SVM to your Active Directory enables your users to use their existing AD\-based identities to authenticate and access FSx for ONTAP over the Network File System \(NFS\) or Server Message Block \(SMB\) protocol\.

## Creating a storage virtual machine<a name="creating-svms"></a>

You can create an FSx for ONTAP SVM using the Amazon FSx console, the AWS CLI, and the Amazon FSx API\.

The maximum number of SVMs you can create for a file system depends on the amount of throughput capacity provisioned\. For more information, see [Storage virtual machines \(SVM\)](how-it-works-fsx-ontap.md#svm-resource)\.

### To create a storage virtual machine \(console\)<a name="create-svm-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **Storage virtual machines**\.

1. Choose **Create new storage virtual machine**\.

   The **Create new storage virtual machine** dialog box appears\.  
![\[The Create new storage virtual machine (SVM) dialog box.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/create-svm.png)

1. For **File system**, choose the file system to create the storage virtual machine on\.

1. In the **Storage virtual machine name** field, provide a name for the storage virtual machine\. You can use a maximum of 47 alphanumeric characters, plus the underscore \(\_\) special character\.

1. For **SVM administrative password**, you can optionally choose **Specify a password** and provide a password for this SVM's `vsadmin` user\. You can use the `vsadmin` user to administer the SVM using the ONTAP CLI or REST API\. For more information about the `vsadmin` user, see [Managing SVMs using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#vsadmin-ontap-cli)\.

   If you choose **Don't specify a password** \(the default\), you can still use the file system's `fsxadmin` user to manage your file system using the ONTAP CLI or REST API, but you can't use your SVM's `vsadmin` user to do the same\.

1. For **Active Directory**, you have the following options:
   + If you don't want to join your file system to an Active Directory, choose **Do not join an Active Directory**\.
   + If you want to join your file system to a self\-managed Active Directory domain, choose **Join an Active Directory**, and provide the following details for your Active Directory:
     + The NetBIOS name of the Active Directory computer object to create for your SVM\. The NetBIOS name cannot exceed 15 characters\.
     + The fully qualified domain name of your Active Directory\. The domain name cannot exceed 255 characters\.
     + **DNS server IP addresses** – The IPv4 addresses of the Domain Name System \(DNS\) servers for your domain\.
     + **Service account username** – The user name of the service account in your existing Active Directory\. Do not include a domain prefix or suffix\. 
     + **Service account password** – The password for the service account\.
     + **Confirm password** – The password for the service account\.
     + \(Optional\) **Organizational Unit \(OU\)** – The distinguished path name of the organizational unit to which you want to join your file system\.
     + **Delegated file system administrators group** – The name of the group in your Active Directory that can administer your file system\.

       If you are using AWS Managed Microsoft AD, you need to specify a group such as AWS Delegated FSx Administrators, AWS Delegated Administrators, or a custom group with delegated permissions to the OU\.

       If you are joining to a self\-managed AD, use the name of the group in your AD\. The default group is `Domain Admins`\.

1. For **SVM root volume security style**, choose the security style for the SVM depending on the type of clients that will access your data\. Choose **Unix \(Linux\)** if you will primarily access your data using Linux clients; choose **NTFS** if you will primarily access your data using Windows clients\.

1. Choose **Confirm** to create the storage virtual machine\.

You can monitor the update progress on the **File systems** detail page, in the **Status** column of the **Storage virtual machines** pane\. The storage virtual machine is ready for use when its status is **Created**\.

### To create a storage virtual machine \(CLI\)<a name="create-svm-cli"></a>
+ To create an FSx for ONTAP storage virtual machine \(SVM\), use the [create\-storage\-virtual\-machine](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-storage-virtual-machine.html) CLI command \(or the equivalent [CreateStorageVirtualMachine](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateStorageVirtualMachine.html) API operation\), as shown in the following example\.

  ```
  aws fsx create-storage-virtual-machine \
      --file-system-id fs-0123456789abcdef0 \
      --name vol1 \
      --svm-admin-password password \
      --active-directory-configuration SelfManagedActiveDirectoryConfiguration='{DomainName="corp.example.com", \
  OrganizationalUnitDistinguishedName="OU=FileSystems,DC=corp,DC=example,DC=com",FileSystemAdministratorsGroup="FSxAdmins", \
  UserName="FSxService",Password="password", \
     DnsIps=["10.0.1.18"]}',NetBiosName=amznfsx12345
  ```

After successfully creating the storage virtual machine, Amazon FSx returns its description in JSON format\.

## Updating a storage virtual machine<a name="updating-svms"></a>

You can update the configuration of an FSx for ONTAP storage virtual machine using the Amazon FSx console, the AWS CLI, and the Amazon FSx API\.

### To update a storage virtual machine \(console\)<a name="update-svm-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, and then choose the ONTAP file system that you want to update a storage virtual machine for\.

1. Choose the **Storage virtual machines** tab\.

1. Choose the storage virtual machine that you want to update\.

1. For **Actions**, choose **Update storage virtual machine**\.

1. For **SVM administrative password**, you can choose **Specify a password** and provide a new password for this SVM's `vsadmin` user\. You can use the `vsadmin` user to administer your SVM using the ONTAP CLI or REST API\. 

   If you choose **Don't specify a password** \(the default\), you can still use the file system's `fsxadmin` user to manage your file system using the ONTAP CLI or REST API, but you can't use your SVM's `vsadmin` user to do the same\.

1. For **Active Directory**, you can update the following properties of your Active Directory configuration:
   + **DNS server IP addresses** – The IPv4 addresses of the Domain Name System \(DNS\) servers for your domain\.
   + **Service account username** – The user name of the service account in your existing Active Directory\. Do not include a domain prefix or suffix\. 
   + **Service account password** – The password for the service account\.

1. Choose **Confirm** to update the storage virtual machine\.

### To update a storage virtual machine \(CLI\)<a name="update-svm-cli"></a>
+ To update the configuration of an FSx for ONTAP volume, use the [update\-storage\-virtual\-machine](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-storage-virtual-machine.html) CLI command \(or the equivalent [UpdateStorageVirtualMachine](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateStorageVirtualMachine.html) API operation\), as shown in the following example\.

  ```
  aws fsx update-storage-virtual-machine \
      --storage-virtual-machine-id svm-5ab87160b8e4ad90d \
      --svm-admin-password new-svm-password \
      --active-directory-configuration SelfManagedActiveDirectoryConfiguration='{UserName="new-user-name", \
        Password="new-password", DnsIps=["10.0.1.28"]}'
  ```

## Deleting a storage virtual machine<a name="deleting-svms"></a>

You can only delete an FSx for ONTAP storage virtual machine \(SVM\) by using the Amazon FSx console, the AWS CLI, and the Amazon FSx API\.

**Important**  
You cannot delete an SVM by using the NetApp ONTAP CLI or API\.

**Note**  
Before you delete a storage virtual machine, make sure that no applications are accessing the data in the SVM that you want to delete and that you have deleted all non\-root volumes\.

### To delete a storage virtual machine \(console\)<a name="delete-svm-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, and then choose the ONTAP file system that you want to delete an SVM from\.

1. Choose the **Storage virtual machines** tab\.

1. Choose the storage virtual machine that you want to delete\.

1. For **Actions**, choose **Delete storage virtual machine**\.

1. In the delete confirmation dialog box, choose **Delete storage virtual machines**\.

### To delete a storage virtual machine \(CLI\)<a name="delete-svm-cli"></a>
+ To delete an FSx for ONTAP storage virtual machine, use the [delete\-storage\-virtual\-machine](https://docs.aws.amazon.com/cli/latest/reference/fsx/delete-storage-virtual-machine.html) CLI command \(or the equivalent [DeleteStorageVirtualMachine](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DeleteStorageVirtualMachine.html) API operation\), as shown in the following example\.

  ```
  aws fsx delete-storage-virtual-machine --storage-virtual-machine-id svm-5ab87160b8e4ad90d
  ```

## Viewing a storage virtual machine<a name="viewing-svms"></a>

You can see the FSx for ONTAP storage virtual machines that are currently on your file system using the Amazon FSx console, the AWS CLI, and the Amazon FSx API\.

**To view a storage virtual machine on your file system:**
+ **Using the console** – Choose a file system to view its **File systems** detail page\. To list all the storage virtual machines on the file system, choose the **Storage virtual machines** tab, and then choose the storage virtual machine that you want to view\.
+ **Using the CLI or API** – Use the [describe\-storage\-virtual\-machines](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-storage-virtual-machines.html) CLI command or the [DescribeStorageVirtualMachines](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeStorageVirtualMachines.html) API operation\.