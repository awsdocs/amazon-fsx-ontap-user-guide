# Keeping your Active Directory configuration updated in Amazon FSx<a name="keep-ad-up-to-date"></a>

To help ensure continued, uninterrupted availability of your Amazon FSx file system, update your SVM's self\-managed Active Directory configuration whenever you change your self\-managed Active Directory setup\.

For example, suppose that your Active Directory uses a time\-based password\-reset policy\. In this case, as soon as the password is reset, make sure to update the service account password with Amazon FSx\. To do this, use the Amazon FSx console, API, or AWS CLI\. Similarly, if the DNS server IP addresses change for your Active Directory domain, as soon as the change occurs, update the DNS server IP addresses with Amazon FSx\. Again, do this using the Amazon FSx console, API, or CLI\.

## Updating an SVM AD configuration<a name="update-svm-ad"></a>

You can use the Amazon FSx console, AWS CLI, or the Amazon FSx API to update the service credentials or the DNS server IP addresses of an SVM's Active Directory configuration\. For more information, see [Updating a storage virtual machine](managing-svms.md#updating-svms)\. 

**Note**  
You cannot change the Active Directory configuration of an existing SVM using the Amazon FSx console, AWS CLI, or the Amazon FSx API\. For example, if an SVM was created without being joined to an Active Directory, you cannot join it to an Active Directory afterwards\.  
You can change the Active Directory configuration of an existing SVM by using the ONTAP CLI or API directly, using the `vserver cifs` commands\. For more information, see [ Manage the CIFS configuration of a Vserver](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-cmpr-9101%2FTOC__vserver__cifs.html)\.