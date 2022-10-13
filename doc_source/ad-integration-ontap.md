# Working with Microsoft Active Directory in FSx for ONTAP<a name="ad-integration-ontap"></a>

Amazon FSx works with Microsoft Active Directory \(AD\) to integrate with your existing environments\. Active Directory is the Microsoft directory service used to store information about objects on the network and make this information easy for administrators and users to find and use\. These objects typically include shared resources such as file servers and network user and computer accounts\.

When you create a storage virtual machine \(SVM\) with Amazon FSx, you can optionally join it to your Active Directory domain to provide user authentication and file\- and folder\-level access control\. Your SMB clients can then use their existing user identities in Active Directory to authenticate themselves and access the SVM's volumes\. Users can also use their existing identities to control access to individual files and folders\. In addition, you can migrate your existing files and folders and these items' security access control list \(ACL\) configuration to Amazon FSx without any modifications\.

When you join Amazon FSx for NetApp ONTAP to an Active Directory, you join each SVM to the Active Directory independently\. This means that you can have a file system where some SVMs are joined to an Active Directory, while other SVMs are not\.

After join your SVM to an Active Directory, you can update the following properties:
+ Service user credentials
+ DNS server IP addresses

**Topics**
+ [Joining Amazon FSx SVMs to a Microsoft Active Directory](self-managed-AD.md)
+ [Prerequisites for using a self\-managed Microsoft AD](self-manage-prereqs.md)
+ [Best practices for joining FSx for ONTAP SVMs to an Active Directory domain](self-managed-AD-best-practices.md)
+ [Joining an FSx for ONTAP SVM to an Active Directory domain](create-joined-svm.md)
+ [Keeping your Active Directory configuration updated in Amazon FSx](keep-ad-up-to-date.md)