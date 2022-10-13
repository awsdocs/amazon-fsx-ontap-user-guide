# Supported clients<a name="supported-clients-fsx"></a>

FSx for ONTAP file systems support accessing data from a wide variety of compute instances and operating systems\. It does this by supporting access using the Network File System \(NFS\) protocol \(v3, v4\.0, v4\.1 and v4\.2\), all versions of the Server Message Block \(SMB\) protocol \(including 2\.0, 3\.0, and 3\.1\.1\), and the Internet Small Computer Systems Interface \(iSCSI\) protocol\.

The following AWS compute instances are supported for use with FSx for ONTAP:
+ Amazon Elastic Compute Cloud \(Amazon EC2\) instances running Linux with NFS or SMB support, Microsoft Windows, and MacOS\. For more information, see [Mounting volumes](attach-volumes.md)\.
+ Amazon Elastic Container Service \(Amazon ECS\) Docker containers on Amazon EC2 Windows and Linux instances\. For more information, see [Using Amazon Elastic Container Service with FSx for ONTAP](mount-ontap-ecs-containers.md)\.
+ Amazon Elastic Kubernetes Service – To learn more, see [Amazon FSx for NetApp ONTAP CSI driver](https://docs.aws.amazon.com/eks/latest/userguide/fsx-ontap.html) in the **Amazon EKS User Guide**\.
+ Red Hat OpenShift Service on AWS \(ROSA\) – To learn more, see [What is Red Hat OpenShift Service on AWS?](https://docs.aws.amazon.com/ROSA/latest/userguide/what-is-rosa.html) in the *Red Hat OpenShift Service on AWS User Guide*\.
+ Amazon WorkSpaces instances\. For more information, see [Using Amazon WorkSpaces with FSx for ONTAP](using-workspaces.md)\.
+ Amazon AppStream 2\.0 instances\.
+ AWS Lambda – For more information, see the AWS blog post [Enabling SMB access for server\-less workloads with Amazon FSx](http://aws.amazon.com/blogs/storage/enabling-smb-access-for-serverless-workloads/)\.
+ Virtual machines \(VMs\) running in VMware Cloud on AWS environments\. For more information, see [Configure Amazon FSx for NetApp ONTAP as External Storage](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vmc-aws-operations/GUID-D55294A3-7C40-4AD8-80AA-B33A25769CCA.html?hWord=N4IghgNiBcIGYGcAeIC+Q) in the *VMware Cloud on AWS* documentation\.

Once mounted, FSx for ONTAP file systems appear as a local directory or drive letter over NFS and SMB, providing fully managed, shared network file storage that can be simultaneously accessed by up to thousands of clients\. iSCSI LUNS are accessible as block devices when mounted over iSCSI\.