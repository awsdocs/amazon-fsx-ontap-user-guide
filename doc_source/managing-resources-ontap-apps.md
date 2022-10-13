# Managing FSx for ONTAP resources using NetApp applications<a name="managing-resources-ontap-apps"></a>

In addition to the AWS Management Console, AWS CLI, and AWS API and SDKs, you can also use these NetApp management tools and applications to manage your FSx for ONTAP resources:

**Topics**
+ [Using NetApp Cloud Manager](#netapp-cloudmgr)
+ [Using the NetApp ONTAP CLI](#netapp-ontap-cli)
+ [Using the NetApp ONTAP REST API](#netapp-ontap-api)

**Important**  
Amazon FSx periodically syncs with ONTAP to ensure consistency\. If you create or modify volumes using NetApp applications, it may take up to several minutes for these changes to be reflected in the AWS Management Console, AWS CLI, API and SDKs\.

## Using NetApp Cloud Manager<a name="netapp-cloudmgr"></a>

NetApp Cloud Manager provides a centralized user interface to manage, monitor, and automate ONTAP deployments in AWS and on premises\. For more information, see the [Cloud Manager documentation](https://docs.netapp.com/us-en/cloud-manager-fsx-ontap/index.html)\.

## Using the NetApp ONTAP CLI<a name="netapp-ontap-cli"></a>

You can manage your Amazon FSx for NetApp ONTAP resources using the NetApp ONTAP CLI\. You can manage resources at the file system \(analogous to NetApp ONTAP cluster\) level, and at the SVM level\.

### Managing file systems with the NetApp ONTAP CLI<a name="fsxadmin-ontap-cli"></a>

You can run NetApp ONTAP CLI commands on your FSx for ONTAP file system, analogous to running them on a NetApp ONTAP cluster\. You access the NetApp ONTAP CLI on your file system by establishing a secure shell \(SSH\) connection to the file system's management endpoint\. You use the `fsxadmin` password you created when you created the file system to log in\. You can find the file system's management endpoint **DNS name** and **IP address** in the Amazon FSx console, in the **Administration** tab of the FSx for ONTAP file system details page, shown in the following graphic\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-admin-panel.png)

To connect to the file system's management endpoint with SSH, use the user `fsxadmin` and the password that you set when you created the file system\. You can SSH into the file system from a client that is in the same VPC as the file system, using the management endpoint IP address or DNS name\.

```
ssh fsxadmin@file-system-management-endpoint-ip-address
```

The SSH command with sample values:

```
ssh fsxadmin@198.51.100.0
```

The SSH command using the management endpoint DNS name:

```
ssh fsxadmin@file-system-management-endpoint-dns-name
```

The SSH command using a sample DNS name:

```
ssh fsxadmin@management.fs-abcdef01234567892.fsx.us-east-2.aws.com
```

```
Password: fsxadmin-password

This is your first recorded login.
FsxIdabcdef01234567892::>
```

The `fsxadmin` user has a view at the file system level, which includes all SVMs and volumes in the file system, equivalent to that of a NetApp ONTAP cluster administrator\. However, you cannot perform most NetApp ONTAP CLI [http://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-cmpr-9101%2FTOC__cluster.html&cp=5_1_10](http://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-cmpr-9101%2FTOC__cluster.html&cp=5_1_10) commands\.

### Managing SVMs using the NetApp ONTAP CLI<a name="vsadmin-ontap-cli"></a>

You can run NetApp ONTAP CLI commands on your FSx for ONTAP SVM by establishing a secure shell \(SSH\) connection to the SVM's management endpoint\. You can use the `fsxadmin` username and password you created on the SVM's file system, or the `vsadmin` username and password, if you specified one when you created the SVM\. You can find the SVM's management endpoint **DNS name** and **IP address** in the Amazon FSx console, in the **Endpoints** panel of the Storage virtual machines details page, shown in the following graphic\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-svm-endpoints.png)

To connect to the SVM's management endpoint with SSH, use username `vsadmin` and the vsadmin password that you set when you created the SVM\. If you did not set a vsadmin password, use username `fsxadmin` and the fsxadmin password\. You can SSH into the SVM from a client that is in the same VPC as the file system, using the management endpoint IP address or DNS name\.

```
ssh vsadmin@svm-management-endpoint-ip-address
```

The command with sample values:

```
ssh vsadmin@198.51.100.10
```

The SSH command using the management endpoint DNS name:

```
ssh vsadmin@svm-management-endpoint-dns-name
```

The SSH command using a sample DNS name:

```
ssh vsadmin@management.svm-abcdef01234567892fs-08fc3405e03933af0.fsx.us-east-2.aws.com
```

```
Password: vsadmin-password

This is your first recorded login.
FsxIdabcdef01234567892::>
```

Amazon FSx for NetApp ONTAP supports the NetApp ONTAP CLI commands\.

For a complete reference of NetApp ONTAP CLI commands, see the [ONTAP Commands: Manual Page Reference](https://docs.netapp.com/us-en/ontap-cli-9111/)\.

## Using the NetApp ONTAP REST API<a name="netapp-ontap-api"></a>

When accessing your Amazon FSx for NetApp ONTAP using the NetApp ONTAP REST API with the `fsxadmin` login, you will need to do one of the following:
+ Disable TLS validation\.
+ Trust the AWS certificate authorities \(CAs\) – The certificate bundle for the CAs in each region can be found at the follow URLs:
  + https://fsx\-aws\-certificates\.s3\.amazonaws\.com/bundle\-*aws\-region*\.pem for Public AWS Regions
  + https://fsx\-aws\-us\-gov\-certificates\.s3\.amazonaws\.com/bundle\-*aws\-region*\.pem for AWSGovCloud Regions
  + https://fsx\-aws\-cn\-certificates\.s3\.amazonaws\.com/bundle\-*aws\-region*\.pem for AWS China Regions

For a complete reference of NetApp ONTAP REST API commands, see the [NetApp ONTAP REST API Online Reference](https://library.netapp.com/ecmdocs/ECMLP2882307/html/index.html)\.