# You can't join a storage virtual machine \(SVM\) to Active Directory<a name="join-svm-to-ad"></a>

If you're unable to join your storage virtual machines \(SVMs\) to your Active Directory, first review [Joining Amazon FSx SVMs to a Microsoft Active Directory](self-managed-AD.md)\. Common problems that prevent an SVM from joining to your Active Directory are listed in the following sections\.

**Topics**
+ [Amazon FSx can't reach the self\-managed Active Directory DNS server or domain controllers](#cannot-reach-domain-controller)
+ [Amazon FSx can't connect to your Active Directory domain controllers because the service account credentials are not valid](#invalid-service-credentials)
+ [Amazon FSx can't connect to your Active Directory domain controllers because of insufficient service account credentials](#insufficient-service-credentials)
+ [Amazon FSx can't connect to the Active Directory domain controllers because the organizational unit specified doesn't exist or isn't accessible](#bad-org-unit-service-credentials)

## Amazon FSx can't reach the self\-managed Active Directory DNS server or domain controllers<a name="cannot-reach-domain-controller"></a>

Creating an SVM joined to your self\-managed Active Directory fails with the following error message:

 Amazon FSx is unable to communicate with your Active Directory domain controller\(s\)\. Please delete your storage virtual machine and create a new one once you've allowed network traffic between Amazon FSx and your domain controller\(s\) as recommended in the Amazon FSx user guide\. 

To troubleshoot and resolve this issue, use the following steps\.

1. Verify that you followed the prerequisites for having network connectivity and routing established between the subnet or subnets where your file system resides, and your self\-managed Active Directory\. For more information, see [Prerequisites for using a self\-managed Microsoft AD](self-manage-prereqs.md)\.

1. Verify that you configured the VPC security groups that you associated with your Amazon FSx file system, along with any VPC network access control lists \(ACLs\), to allow outbound network traffic on all ports\.
**Note**  
If you want to implement least privilege, you can allow outbound traffic only to the specific ports required for communication with the Active Directory domain controllers\. For more information, see the [Microsoft Active Directory documentation](https://docs.microsoft.com/en-US/troubleshoot/windows-server/identity/config-firewall-for-ad-domains-and-trusts)\.

1. Verify that your Active Directory domain's domain name system \(DNS\) servers and domain controllers are active and able to respond to requests for the domain provided\.

1. Make sure that the firewall rules on your Active Directory domain's domain controllers allow traffic from your Amazon FSx file system\. For more information, see the [Microsoft Active Directory documentation](https://docs.microsoft.com/en-US/troubleshoot/windows-server/identity/config-firewall-for-ad-domains-and-trusts)\.

## Amazon FSx can't connect to your Active Directory domain controllers because the service account credentials are not valid<a name="invalid-service-credentials"></a>

Creating an SVM joined to your self\-managed Active Directory fails with the following error message:

 Amazon FSx is unable to establish a connection with your Active Directory domain controller\(s\) because the service account credentials provided are invalid\. To fix this problem, delete your storage virtual machine and create a new one using a valid service account as recommended in the Amazon FSx user guide\. 

To troubleshoot and resolve this issue, use the following steps\.

1. In the self\-managed Active Directory configuration, verify that you're entering only the user name as input for the **Service account username**, such as `ServiceAcct`\.
**Important**  
When entering the service account user name, do not include a domain prefix \(for example, `corp.com\ServiceAcct`\) or domain suffix \(for example, `ServiceAcct@corp.com`\)\. Do not use the distinguished name \(DN\) when entering the service account user name \(for example, `CN=ServiceAcct,OU=example,DC=corp,DC=com`\)\. 

1. Verify that the service account that you provided exists in your Active Directory domain\.

1. Make sure that you delegated the required permissions to the service account that you provided\. The service account must be able to create and delete computer objects in the organizational unit \(OU\) in the domain to which you're joining the file system\. The service account also needs, at a minimum, to have permissions to do the following:
   + Reset passwords
   + Restrict accounts from reading and writing data
   + Validated ability to write to the DNS hostname
   + Validated ability to write to the service principal name

   For more information about creating a service account with correct permissions, see [Delegating privileges to your Amazon FSx service account](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

## Amazon FSx can't connect to your Active Directory domain controllers because of insufficient service account credentials<a name="insufficient-service-credentials"></a>

Creating an SVM joined to your self\-managed Active Directory fails with the following error message:

 Amazon FSx is unable to establish a connection with your Active Directory domain controller\(s\)\. This is due to either the port requirements for the Active Directory have not been met, or the service account provided does not have permission to join the storage virtual machine to the domain with the specified organizational unit\. To fix this problem, delete your storage virtual machine and create a new one referring to configuration requirements specified in the Amazon FSx user guide\. 

To resolve this issue, make sure that you delegated the required permissions to the service account that you provided\. The service account must be able to create and delete computer objects in the OU in the domain to which you're joining the file system\. The service account also needs, at a minimum, to have permissions to do the following:
+ Reset passwords
+ Restrict accounts from reading and writing data
+ Validated ability to write to the DNS hostname
+ Validated ability to write to the service principal name

For more information about creating a service account with correct permissions, see [Delegating privileges to your Amazon FSx service account](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

## Amazon FSx can't connect to the Active Directory domain controllers because the organizational unit specified doesn't exist or isn't accessible<a name="bad-org-unit-service-credentials"></a>

Creating an SVM joined to your self\-managed Active Directory fails with the following error message:

 Amazon FSx is unable to establish a connection with your Active Directory domain controller\(s\)\. This is because the organizational unit you specified either doesn't exist or isn't accessible to the service account provided\. To fix this problem, delete your storage virtual machine and create a new one specifying an organizational unit to which the service account can join the storage virtual machine as recommended in the Amazon FSx user guide\. 

To troubleshoot and resolve this issue, use the following steps\.

1. Verify that the OU you provided is in the Active Directory domain\.

1. Make sure that you delegated the required permissions to the service account that you provided\. The service account must be able to create and delete computer objects in the OU in the domain to which you're joining the file system\. The service account also needs, at a minimum, to have permissions to do the following in the OU that you provided:
   + Reset passwords
   + Restrict accounts from reading and writing data
   + Validated ability to write to the DNS hostname
   + Validated ability to write to the service principal name

   For more information about creating a service account with correct permissions, see [Delegating privileges to your Amazon FSx service account](self-managed-AD-best-practices.md#connect_delegate_privileges)\.