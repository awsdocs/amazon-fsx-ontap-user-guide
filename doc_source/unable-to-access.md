# You can't access your file system<a name="unable-to-access"></a>

There are a number of potential causes for being unable to access your file system, each with their own resolution, as follows\.

**Topics**
+ [The file system's elastic network interface was modified or deleted](#eni-deleted)
+ [The file system's security group lacks the required inbound rules](#sg-lacks-inbound-rules)
+ [The compute instance's security group lacks the required outbound rules](#compute-instance-lacks-inbound-rules)
+ [The compute instance's subnet doesn't use any of the route tables associated with your file system](#subnet-route-tables)
+ [Can't access a file system over iSCSI from a client in another VPC](#file-system-iscsi)
+ [Can't access a file system over NFS, SMB, the ONTAP CLI, or the ONTAP REST API from a client in another VPC or on\-premises](#unable-to-access-over-network)

## The file system's elastic network interface was modified or deleted<a name="eni-deleted"></a>

You must not modify or delete any of the file system's elastic network interfaces\. Modifying or deleting a network interface can cause a permanent loss of connection between your virtual private cloud \(VPC\) and your file system\. Create a new file system, and do not modify or delete the Amazon FSx network interface\. For more information, see [ File System Access Control with Amazon VPC ](limit-access-security-groups.md)\.

## The file system's security group lacks the required inbound rules<a name="sg-lacks-inbound-rules"></a>

Review the inbound rules specified in [Amazon VPC security groups](limit-access-security-groups.md#fsx-vpc-security-groups), and make sure that the security group associated with your file system has the corresponding inbound rules\. 

## The compute instance's security group lacks the required outbound rules<a name="compute-instance-lacks-inbound-rules"></a>

Review the outbound rules specified in [Amazon VPC security groups](limit-access-security-groups.md#fsx-vpc-security-groups), and make sure that the security group associated with your compute instance has the corresponding outbound rules\.

## The compute instance's subnet doesn't use any of the route tables associated with your file system<a name="subnet-route-tables"></a>

FSx for ONTAP creates endpoints for accessing your file system in a VPC route table\. We recommend that you configure your file system to use all of the VPC route tables that are associated with the subnets in which your clients are located\. By default, Amazon FSx uses your VPC's main route table\. You can optionally specify one or more route tables for Amazon FSx to use when you create your file system\.

If you can ping your file system's Intercluster endpoint but cannot ping your file system's Management endpoint \(see [File system endpoints](how-it-works-fsx-ontap.md#manage-file-system) for more information\), your client is likely not in a subnet that's associated with one of your file system's route tables\. To access your file system, associate one of your file system's route tables with your client's subnet\.

## Can't access a file system over iSCSI from a client in another VPC<a name="file-system-iscsi"></a>

To access a file system over the Internet Small Computer Systems Interface \(iSCSI\) protocol from a client in another VPC, you can configure Amazon VPC peering or AWS Transit Gateway between the VPC associated with your file system and the VPC in which your client resides\. For more information, see [Create and accept VPC peering connections](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html) in the *Amazon Virtual Private Cloud* guide\. 

## Can't access a file system over NFS, SMB, the ONTAP CLI, or the ONTAP REST API from a client in another VPC or on\-premises<a name="unable-to-access-over-network"></a>

To access a file system over Network File System \(NFS\), Server Message Block \(SMB\), or the NetApp ONTAP CLI and REST API from a client in another VPC or on premises, you must configure routing using AWS Transit Gateway between the VPC associated with your file system and the network in which your client resides\. For more information, see [Accessing data](supported-fsx-clients.md)\.