# Data encryption in FSx for ONTAP<a name="encryption"></a>

Amazon FSx for NetApp ONTAP supports encryption of data at rest and encryption of data in transit\. Encryption of data at rest is automatically enabled when creating an Amazon FSx file system\. Amazon FSx for NetApp ONTAP also supports Kerberos\-based encryption in transit over the NFS and SMB protocols if you're accessing data in a Storage Virtual Machine \(SVM\) that's joined to an Active Directory or to a domain using the Lightweight Directory Access Protocol \(LDAP\)\.

**Topics**
+ [When to use encryption](#whenencrypt)

## When to use encryption<a name="whenencrypt"></a>

If your organization is subject to corporate or regulatory policies that require encryption of data and metadata at rest, your data is automatically encrypted at rest, and we recommend that you enable encryption of data in transit by mounting your file system using encryption of data in transit\.

For more information on encryption with Amazon FSx for NetApp ONTAP, see these related topics:
+ [Getting started: Create your Amazon FSx for NetApp ONTAP file system](getting-started-step1.md)
+ [Creating Amazon FSx file systems](managing-file-systems.md#creating-file-systems)