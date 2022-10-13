# Encryption of data in transit<a name="encryption-in-transit"></a>

Encryption of data in transit using Kerberos is supported for SMB 3\.0 or newer, and NFSv3 and NFSv4 protocols\. To enable encryption in transit using Kerberos for the NFS protocol, see [Using Kerberos with NFS for strong security](https://docs.netapp.com/us-en/ontap/pdfs/sidebar/Using_Kerberos_with_NFS_for_strong_security.pdf) in the NetApp ONTAP Documentation Center\. The following section describes how to enable encryption in transit using Kerberos for the SMB protocol\.

## Encrypting data in transit with SMB<a name="SMB-encryption"></a>

Encryption of data in transit is supported on file shares that are mapped on a compute instance that supports SMB protocol 3\.0 or newer\. This includes all Microsoft Windows versions starting from Windows Server 2012 and Windows 8\. When enabled, FSx for ONTAP automatically encrypts data in transit using SMB encryption as you access your file system without the need for you to modify your applications\.

FSx for ONTAP SMB supports 128 or 256 bit encryption, depending on the client session request\. For descriptions of the different encryption levels, see the *Set the SMB server minimum authentication security level* section of [Manage SMB with the CLI](https://docs.netapp.com/us-en/ontap/pdfs/sidebar/Manage_SMB_with_the_CLI.pdf) on the NetApp ONTAP Documentation Center\. Note that it is the client that determines the encryption algorithm\. Both NTLM and Kerberos authentication work with both 128 and 256 bit encryption\. The FSx for ONTAP SMB Server will accept all standard Windows client requests, and the granular controls are handled by Microsoft Group Policy or Registry settings\.

You use the NetApp ONTAP CLI to manage the encryption in transit settings on FSx for ONTAP SVMs and volumes\. To access the NetApp ONTAP CLI, establish an SSH session on the SVM on which you are making encryption in transit settings, as described in [Managing SVMs using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#vsadmin-ontap-cli)\.

### Enable SMB encryption of data in transit<a name="enable-smb-encryption"></a>

By default, when you create an SVM, SMB encryption is turned off\. You can either enable SMB encryption required on individual shares, or on an SVM, which turns it on for all shares on that SVM\.

**Note**  
When SMB encryption required is enabled on an SVM or share, SMB clients that do not support encryption cannot connect to that SVM or share\.

**To require SMB encryption for incoming SMB traffic on an SVM**

Use the following procedure to require SMB encryption on a SVM using the NetApp ONTAP CLI\.

1. Establish a secure shell \(SSH\) connection to the SVM's management endpoint as described in [Managing SVMs using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#vsadmin-ontap-cli)\.

1. Use the following NetApp ONTAP CLI command to require SMB encryption for incoming SMB traffic to the SVM\.

   ```
   vserver cifs security modify -vserver vserver_name -is-smb-encryption-required true
   ```

1. To stop requiring SMB encryption for incoming SMB traffic, use the following command\.

   ```
   vserver cifs security modify -vserver vserver_name -is-smb-encryption-required false
   ```

1. To see the current `is-smb-encryption-required` setting on an SVM, use the following NetApp ONTAP CLI command:

   ```
   vserver cifs security show -vserver vs1 -fields is-smb-encryption-required
            
   vserver  is-smb-encryption-required
   -------- -------------------------
   vs1      true
   ```

For more information about managing SMB encryption on an SVM, see [ Configuring required SMB encryption on SMB servers for data transfers over SMB](https://docs.netapp.com/us-en/ontap/smb-admin/configure-required-encryption-concept.html) in the NetApp ONTAP Documentation Center\.

**To enable SMB encryption on a volume**

Use the following procedure to enable SMB encryption on a share using the NetApp ONTAP CLI\.

1. Establish a secure shell \(SSH\) connection to the SVM's management endpoint as described in [Managing SVMs using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#vsadmin-ontap-cli)\.

1. Use the following NetApp ONTAP CLI command to create a new SMB share and require SMB encryption when accessing this share\.

   ```
   vserver cifs share create -vserver vserver_name -share-name share_name -path share_path -share-properties encrypt-data
   ```

   For more information, see [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__create.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__create.html) in the NetApp ONTAP CLI Command man pages\.

1. To require SMB encryption on an existing SMB share, use the following command\.

   ```
   vserver cifs share properties add vserver_name -share-name share_name -share-properties encrypt-data
   ```

   For more information, see [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__properties__add.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__properties__add.html) in the NetApp ONTAP CLI Command man pages\.

1. To turn off SMB encryption on an existing SMB share, use the following command\.

   ```
   vserver cifs share properties remove -vserver vserver_name -share-name share_name encrypt-data
   ```

   For more information, see [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__properties__remove.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__properties__remove.html) in the NetApp ONTAP CLI Command man pages\.

1. To see the current `is-smb-encryption-required` setting on an SMB share, use the following NetApp ONTAP CLI command:

   ```
   vserver cifs share properties show -vserver vserver_name -share-name share_name -fields share-properties
   ```

   If one of the properties returned by the command is the `encrypt-data` property, then that property specifies that SMB encryption must be used when accessing this share\.

   For more information, see [https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__properties__show.html](https://docs.netapp.com/ontap-9/topic/com.netapp.doc.dot-cm-cmpr-9101/vserver__cifs__share__properties__show.html) in the NetApp ONTAP CLI Command man pages\.