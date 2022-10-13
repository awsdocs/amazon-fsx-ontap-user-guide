# Accessing data from within AWS<a name="access-environments"></a>

Each Amazon FSx file system is associated with a Virtual Private Cloud \(VPC\)\. You can access your FSx for ONTAP file system from anywhere in the VPC it is deployed in regardless of Availability Zone\. You can also access your file system from other VPCs\. These VPCs can be in different accounts or regions\. In addition to any requirements listed below for accessing different FSx for ONTAP resources, you also need to ensure that your file system's VPC security group allows data to flow between your file system and clients; for a list of required ports, see [Amazon VPC security groups](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/limit-access-security-groups.html#fsx-vpc-security-groups)\. 

## Accessing data from within the same VPC<a name="same-vpc-access"></a>

When you create your Amazon FSx for NetApp ONTAP file system, you select the Amazon VPC in which it is located\. All SVM's and volumes associated with the Amazon FSx for NetApp ONTAP file system are also located in the same VPC\. When the file system and the client mounting the storage virtual machine \(SVM\) volume are located in the same VPC and AWS account, you can mount a volume using the SVM's DNS name and volume junction or SMB share, depending on the client\. For more information, see [Mounting volumes](attach-volumes.md)\.

You can achieve optimal performance by accessing an SVM volume using a client that is located in the same Availability Zone as the file system's preferred subnet\. To identify a file system's preferred subnet, in the Amazon FSx console, choose **File systems**, then choose the ONTAP file system whose volume you are mounting, and the preferred subnet is displayed in the **Preferred subnet** panel\.

## Accessing data from outside the deployment VPC<a name="access-from-outside-deployment-vpc"></a>

This section describes how to access FSx for ONTAP file systems from AWS locations outside of your deployment VPC\.

### Accessing NFS, SMB, or the ONTAP CLI and REST API<a name="vpc-peering"></a>

For Multi\-AZ file systems, the endpoints used to access Amazon FSx for NetApp ONTAP over NFS or SMB, or for administering file systems using the ONTAP CLI or REST API, are floating IP addresses that are created in the VPC route tables you associate with your file system\. These IP addresses are within an `EndpointIpAddressRange` that you can specify when creating a file system\. By default, Amazon FSx chooses an IP address range for you from within the `198.19.0.0/16` IP address range\. The floating IP address allows for management, NFS, and SMB traffic to seamlessly failover between your preferred and standby file servers\. More information is available in [Failover process for FSx for ONTAP](high-availability-AZ.md#MultiAZ-Failover)\. Because the NFS, SMB, and management endpoints use a floating IP, [AWS Transit Gateway](https://aws.amazon.com/transit-gateway/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc)is required to access those interfaces outside of the VPC your FSx for ONTAP file system is deployed in\. This is because VPC Peering doesn’t support routing to floating IPs \(also known as transitive peering\)\. 

For Single\-AZ file systems, the endpoints used to access FSx for ONTAP over NFS or SMB, or for administering file systems using the ONTAP CLI or REST API, are secondary IP addresses on the ENI of the active file server\. The secondary IP addresses are within the VPC’s CIDR range, enabling access to data using VPC Peering, Direct Connect, or VPN without requiring AWS Transit Gateway\. 

#### When is Transit Gateway required?<a name="when-is-transit-gateway-required"></a>

Whether or not Transit Gateway is required for your Multi\-AZ file systems depends on the method you use to access your file system data\. Single\-AZ file systems do not require Transit Gateway\. 


| Data access | Requires Transit Gateway? | 
| --- | --- | 
|  Accessing FSx over NFS, SMB, or the NetApp ONTAP REST API /CLI  |  Only if: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/access-environments.html)  | 
| Accessing data over iSCSI | No | 
| Joining an SVM to an Active Directory | No | 
| SnapMirror | No | 
| FlexCache Caching | No | 
| Global File Cache | No | 

#### Configuring routing using AWS Transit Gateway<a name="configuring-routing-using-AWSTG"></a>

If needed, you can configure Transit Gateway for your Multi\-AZ file systems\. Single\-AZ file systems do not require Transit Gateway\. 

**To configure routing using AWS Transit Gateway**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Choose the FSx for ONTAP file system for which you are configuring access from a peered network\.

1. In **Network & security** copy the **Endpoint IP address range**\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-fs-endpoint-ip-range.png)

1. Add a route to Transit Gateway that routes traffic destined for this IP address range to your file system's VPC\. For more information, see [Working with transit gateways](https://docs.aws.amazon.com/vpc/latest/tgw/working-with-transit-gateways.html) in the *Amazon VPC Transit Gateways*\.

1. Confirm that you can access your FSx for ONTAP file system from the peered network\.

**Note**  
DNS records for the management, NFS, and SMB endpoints are only resolvable from within the same VPC as the file system\. In order to mount a volume or connect to a management port from another network, you need to use the endpoint's IP address\. These IP addresses do not change over time\.

#### Accessing iSCSI or inter\-cluster endpoints outside of the deployment VPC<a name="access-iscsi-or-inter-cluster-endpoints-outside-deployment-vpc"></a>

You can use either VPC Peering or AAWS Transit Gateway to access your file system from outside of the deployment VPC\. VPC Peering can be used to route iSCSI and inter\-cluster traffic between VPCs\. A VPC peering connection is a networking connection between two VPCs\. This type of connection enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) addresses\. You can use VPC peering to connect VPCs within the same AWS Region or between different AAWS Regions\. For more information on VPC peering, see [What is VPC peering?](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html) in the *Amazon VPC Peering Guide*\. 

#### Accessing Multi\-AZ file systems<a name="access-for-multi-az"></a>

For Multi\-AZ file systems, the endpoints used to access Amazon FSx for NetApp ONTAP over NFS or SMB, or for administering file systems using the ONTAP CLI or REST API, are floating IP addresses that are created in the VPC route tables you associate with your file system\. These IP addresses are within an `EndpointIpAddressRange` that you can specify when creating a file system\. By default, Amazon FSx chooses an IP address range for you from within the 198\.19\.0\.0/16 IP address range\.

To access these floating IP address endpoints from a peered network, you need to configure your peered network to route traffic destined to your file system's `EndpointIpAddressRange` to the VPC in which your file system is created\. Alternatively, if you are using NetApp Global File Cache or NetApp FlexCache for remote office caching, both of these technologies communicate with your FSx for ONTAP file system using your file system's inter\-cluster endpoint, which is not a floating IP address\. As a result, you don't need to configure Transit Gateway if all of your clients are accessing Amazon FSx using one of these caching technologies\.

For example, to configure routing using AWS Transit Gateway, use the following procedure\.

**To configure routing using AWS Transit Gateway**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Choose the FSx for ONTAP file system for which you are configuring access from a peered network\.

1. In **Network & security>** copy the **Endpoint IP address range**\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-fs-endpoint-ip-range.png)

1. Add a route to Transit Gateway that routes traffic destined for this IP address range to your file system's VPC\. For more information, see [Working with transit gateways](https://docs.aws.amazon.com/vpc/latest/tgw/working-with-transit-gateways.html) in the *Amazon VPC Transit Gateways*\.

1. Confirm that you can access your FSx for ONTAP file system from the peered network\.

**Note**  
DNS records for the management, NFS, and SMB endpoints are only resolvable from within the same VPC as the file system\. In order to mount a volume or connect to a management port from another network, you need to use the endpoint's IP address\. These IP addresses do not change over time\.

#### Accessing Single\-AZ file systems<a name="access-for-single-az"></a>

For Single\-AZ file systems, the endpoints used to access FSx for ONTAP over NFS or SMB, or for administering file systems using the ONTAP CLI or REST API, are secondary IP addresses on the ENI of the active file server\. The secondary IP addresses are within the VPC’s CIDR range, enabling access to data using VPC Peering, Direct Connect, or VPN without requiring AWS Transit Gateway\.