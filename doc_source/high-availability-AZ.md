# Availability and durability<a name="high-availability-AZ"></a>

Amazon FSx for NetApp ONTAP has two deployment types, Single\-AZ and Multi\-AZ, that offer you different levels of availability and durability\. For information on the service's availability SLA \(Service Level Agreement\), see [Amazon FSx Service Level Agreement](https://aws.amazon.com/fsx/sla/)\.

## Choosing a file system deployment type<a name="choosing-deoloyment-type"></a>

### <a name="deployment-type-features-summary"></a>

When you create a Single\-AZ file system, Amazon FSx automatically provisions a pair of file servers in separate fault domains within an Availability Zone in an active\-standby configuration\. During planned file system maintenance or unplanned service disruption, Amazon FSx fails over to the standby file server, typically within a few seconds, allowing you to continue accessing your data without any manual intervention\. To ensure high availability, Amazon FSx continuously monitors for hardware failures, and automatically replaces infrastructure components in the event of a failure\. To achieve high durability, Amazon FSx automatically replicates your data within an Availability Zone to protect it from component failure, and offers automatic daily backups that are stored across multiple Availability Zones, providing Multi\-AZ resiliency for all backup data\.

Multi\-AZ file systems support all the availability and durability features of Single\-AZ file systems\. In addition, they are designed to provide continuous availability to data even when an Availability Zone is unavailable\. In a Multi\-AZ deployment, the standby file server is deployed in a different Availability Zone from the active file server in the same AWS Region\. Any changes written to your file system are synchronously replicated across Availability Zones to the standby\.

Multi\-AZ file systems are designed for use cases such as business\-critical production workloads that require high availability to shared ONTAP file data and need storage with built\-in replication across Availability Zones\. Single\-AZ file systems are designed for use cases that do not require the data resiliency model of a Multi\-AZ file system\. They provide a cost\-optimized solution for use cases such as development and test environments, or storing secondary copies of data that is already stored on premises or in other AWS Regions, by only replicating data within an Availability Zone\.

## Failover process for FSx for ONTAP<a name="MultiAZ-Failover"></a>

File systems automatically fail over from the preferred file server to the standby file server if any of the following conditions occur:
+ The preferred file server becomes unavailable
+ The file system's throughput capacity is changed
+ The preferred file server undergoes planned maintenance
+ An Availability Zone outage occurs \(Multi\-AZ file systems only\)

 When failing over from one file server to another, the new active file server automatically begins serving all file system read and write requests\. When the preferred file server is available, Amazon FSx automatically fails back to it\. A failover typically completes in less than 60 seconds from the detection of the failure on the active file server to the promotion of the standby file server to active status\. Failback completes in less than 60 seconds, and only occurs once the preferred file server is fully recovered\. Because the endpoint IP address that clients use to access data over NFS or SMB remains the same, failovers are transparent to Linux, Windows, and macOS applications, which resume file system operations without manual intervention\.

### Testing failover on a file system<a name="testing-failover"></a>

You can test failover on your file system by modifying its throughput capacity\. When you modify your file system's throughput capacity, Amazon FSx switches out the file system's file servers serially\. File systems automatically fail over to the secondary server while Amazon FSx replaces the preferred file server first\. Once updated, the file system automatically fails back to the new primary server and Amazon FSx replaces the secondary file server\.

You can monitor the progress of the throughput capacity update request in the Amazon FSx console, the CLI, and the API\. For more information about modifying your file system's throughput capacity and monitoring the progress of the request, see [Managing throughput capacity](managing-throughput-capacity.md)\.

## Working with file systems<a name="single-multi-az-resources"></a>

### Subnets<a name="fs-subnets"></a>

When you create a VPC, it spans all the Availability Zones in the Region\. Availability Zones are distinct locations that are engineered to be isolated from failures in other Availability Zones\. After creating a VPC, you can add one or more subnets in each Availability Zone\. The default VPC has a subnet in each Availability Zone\. Each subnet must reside entirely within one Availability Zone and cannot span zones\. 

When you create a Single\-AZ file system, you specify a single subnet for the file system\. The subnet you choose defines the Availability Zone in which the file system is created\. 

When you create a Multi\-AZ file system, you specify two subnets, one for the preferred file server, and one for the standby file server\. The two subnets you choose must be in different Availability Zones within the same AWS Region\.

**Note**  
Regardless of the subnet that you specify, you can access your file system from within any subnet in your VPC\.

### File system elastic network interfaces<a name="file-system-eni-fsxw"></a>

 When you create an Amazon FSx file system, Amazon FSx provisions an [elastic network interface](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ElasticNetworkInterfaces.html) \(ENI\) in each of the subnets that you associate with your file system\. The network interface allows your client to communicate with the FSx for ONTAP file system\. The network interfaces are considered to be within the service scope of Amazon FSx, despite being part of your account's VPC\.

**Warning**  
You must not modify or delete the elastic network interfaces associated with your file system\. Modifying or deleting the network interface can cause a permanent loss of connection between your VPC and your file system\.
The elastic network interfaces associated with your file system will have routes automatically created and added to your default VPC and subnet route tables\. Modifying or deleting these routes may cause temporary or permanent loss of connectivity for your file system clients\.

The following table summarizes the subnet, elastic network interface, and IP address resources for FSx for ONTAP file system deployment types:


|  | Single\-AZ | Multi\-AZ | 
| --- | --- | --- | 
| Number of subnets | 1 | 2 | 
| Number of elastic network interfaces | 2 | 2 | 
| Number of IP addresses per ENI | 1 \+ the number of SVMs in the file system | 1 \+ the number of SVMs in the file system | 
| Number of VPC route table routes | N/A | 1 \+ the number of SVMs in the file system | 

Once a file system or SVM is created, its IP addresses don't change until the file system is deleted\.

**Important**  
Amazon FSx doesn't support accessing file systems from, or exposing file systems to the public Internet\. 