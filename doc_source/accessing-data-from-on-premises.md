# Accessing data from on\-premises<a name="accessing-data-from-on-premises"></a>

You can access your FSx for ONTAP file systems from on\-premises using [AWS VPN](https://aws.amazon.com/vpn/) and [AWS Direct Connect](https://aws.amazon.com/getting-started/projects/connect-data-center-to-aws/); more specific use case guidelines are available in the following sections\. In addition to any requirements listed below for accessing different FSx for ONTAP resources from on\-premises, you also need to ensure that your file system's VPC security group allows data to flow between your file system and clients; for a list of required ports, see [Amazon VPC security groups](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/limit-access-security-groups.html#fsx-vpc-security-groups)\.

## Accessing NFS, SMB, or the ONTAP CLI or REST API endpoints from on\-premises<a name="access-NFS-SMB-or-CLI-API-endpoints-from-on-premises"></a>

### Accessing Multi\-AZ file systems<a name="access-multi-az-fs"></a>

Amazon FSx requires that you use AWS Transit Gateway or that you configure remote NetApp Global File Cache or NetApp FlexCache to access Multi\-AZ file systems from an on\-premises network\. In order to support failover across AZs for Multi\-AZ file systems, Amazon FSx uses floating IP addresses for the interfaces used for NFS, SMB, and management traffic \(ONTAP CLI and REST API\)\. Because the NFS, SMB, and management endpoints use floating IPs, you must use [AWS Transit Gateway](https://aws.amazon.com/transit-gateway/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc) in conjunction with AWS Direct Connect or AWS VPN to access these interfaces from an on\-premises network\. The floating IP addresses used for these interfaces are within the `EndpointIpAddressRange` you specify when creating your Multi\-AZ file system\. By default, Amazon FSx chooses an IP address range for you within the `198.19.9.9/16` range\. The floating IP addresses are used to enable a seamless transition of your clients to the standby file system in the event a failover is required\. For more information, see [Failover process for FSx for ONTAP](high-availability-AZ.md#MultiAZ-Failover)\.

**To configure AWS Transit Gateway for access from outside of your VPC**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Choose the FSx for ONTAP file system for which you are configuring access from a peered network\.

1. In **Network & security>** copy the **Endpoint IP address range**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/config-routing-on-prem.png)

1. Add a route to the Transit Gateway that routes traffic destined for this IP address range to your file system's VPC\. For more information, see [Work with transit gateways](https://docs.aws.amazon.com/vpc/latest/tgw/working-with-transit-gateways.html) in the *Amazon VPC Transit Gateway*\.

1. Confirm that you can access your FSx for ONTAP file system from the peered network\.

### Accessing Single\-AZ file systems<a name="access-single-az-fs"></a>

The requirement to use AWS Transit Gateway to access data from an on\-premises network doesn’t exist for Single\-AZ file systems\. Single\-AZ file systems are deployed in a single subnet, and a floating IP address is not required to provide failover between nodes\. Instead, the IP addresses you access on Single\-AZ file systems are implemented as secondary IP addresses within the file system’s VPC CIDR range, enabling you to access your data from another network without requiring AWS Transit Gateway\.

## Accessing inter\-cluster endpoints from on\-premises<a name="access-inter-cluster-endpoints-from-on-premises"></a>

FSx for ONTAP’s inter\-cluster endpoints are dedicated to replication traffic between NetApp ONTAP file systems, including between on\-premises NetApp deployments and FSx for ONTAP\. Replication traffic includes SnapMirror, FlexCache, and FlexClone relationships between storage virtual machines \(SVMs\) and volumes across different file systems, and NetApp Global File Cache\. The inter\-cluster endpoints are also used for Active Directory traffic\. 

The inter\-cluster endpoints of your file system are IP addresses within the CIDR range of the VPC you provide when you create your FSx for ONTAP file system, so Transit Gateway is not required for routing inter\-cluster traffic between on\-premises and the AWS Cloud\. However, on\-premises clients still must use AWS VPN or AWS Direct Connect to establish a secure connection to your VPC\. 