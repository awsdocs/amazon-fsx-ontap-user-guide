# Amazon FSx for NetApp ONTAP performance<a name="performance"></a>

Following is an overview of Amazon FSx for NetApp ONTAP file system performance, with a discussion of the available performance and throughput options and useful performance tips\.

**Topics**
+ [Overview](#perf-overview)
+ [Performance details](#performance-details-fsxw)

## Overview<a name="perf-overview"></a>

File system performance is measured by its latency, throughput, and I/O operations per second \(IOPS\)\.

### Latency<a name="latency-fsxW"></a>

Amazon FSx for NetApp ONTAP provides sub\-millisecond file operation latencies with solid state drive \(SSD\) storage, and tens of milliseconds of latency for capacity pool storage\. Above that, Amazon FSx has two layers of read caching on each file server—NVMe \(non\-volatile memory express\) drives and in\-memory—to provide even lower latencies when you access your most frequently\-read data\.

### Throughput and IOPS<a name="throughput-and-iops-fsxw"></a>

Each Amazon FSx file system provides up to multiple GB/s of throughput and hundreds of thousands of IOPS\. The specific amount of throughput and IOPS that your workload can drive on your file system depends on the throughput capacity and storage capacity configuration of your file system, along with the nature of your workload, including the size of the active working set\.

### SMB Multichannel and NFS nconnect support<a name="single-client-performance"></a>

With Amazon FSx, you can configure SMB Multichannel to provide multiple connections between ONTAP and clients in a single SMB session\. SMB Multichannel uses multiple network connections between the client and server simultaneously to aggregate network bandwidth for maximal utilization\. For information on using the NetApp ONTAP CLI to configure SMB Multichannel, see [Configuring SMB Multichannel for performance and redundancy](https://docs.netapp.com/us-en/ontap/smb-admin/configure-multichannel-performance-task.html)\.

NFS clients can use the `nconnect` mount option to have multiple TCP connections \(up to 16\) associated with a single NFS mount\. Such an NFS client multiplexes file operations onto multiple TCP connections in a round\-robin fashion and thus obtains higher throughput from the available network bandwidth\. NFSv3 and NFSv4\.1\+ support `nconnect`\. [ Amazon EC2 instance network bandwidth](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-network-bandwidth.html) describes the full duplex 5 Gbps per network flow bandwidth limit\. You can overcome this limit by using multiple network flows with `nconnect` or SMB multichannel\. See your NFS client documentation to confirm whether `nconnect` is supported in your client version\. For more information about NetApp ONTAP support for `nconnect`, see [ ONTAP support for NFSv4\.1](https://docs.netapp.com/us-en/ontap/nfs-admin/ontap-support-nfsv41-concept.html)\.

## Performance details<a name="performance-details-fsxw"></a>

To understand the Amazon FSx for NetApp ONTAP performance model in detail, you can examine the architectural components of an Amazon FSx file system\. Your client compute instances, whether they exist in AWS or on\-premises, access your file system through one or multiple elastic network interfaces \(ENI\)\. These network interfaces reside in the Amazon VPC that you associate with your file system\. Behind each file system ENI is an NetApp ONTAP file server that is serving data over the network to the clients accessing the file system\. Amazon FSx provides a fast in\-memory cache and NVMe cache on each file server to enhance performance for the most frequently accessed data\. Attached to each file server are the disks hosting your file system data\.

These components are illustrated in the following diagram\.

![\[FSx for ONTAP architecture.\]](http://docs.aws.amazon.com/fsx/latest/ONTAPGuide/images/fsx-ontap-performance.png)

Corresponding with these architectural components–network interface, in\-memory cache, NVMe cache, and storage volumes–are the primary performance characteristics of an Amazon FSx for NetApp ONTAP file system that determine the overall throughput and IOPS performance\.
+ Network I/O performance: throughput/IOPS of requests between the clients and the file server \(in aggregate\)
+ In\-memory and NVMe cache size on the file server: size of active working set that can be accommodated for caching
+ Disk I/O performance: throughput/IOPS of requests between the file server and the storage disks

There are two factors that determine these performance characteristics for your file system: the amount of SSD IOPS and throughput capacity that you configure for it\. The first two performance characteristics – network I/O performance and in\-memory and NVMe cache size – are solely determined by throughput capacity, while the third one – disk I/O performance – is determined by a combination of throughput capacity and SSD IOPS\.

File\-based workloads are typically spiky, characterized by short, intense periods of high I/O with plenty of idle time between bursts\. To support spiky workloads, in addition to the baseline speeds that a file system can sustain 24/7, Amazon FSx provides the capability to burst to higher speeds for periods of time for both network I/O and disk I/O operations\. Amazon FSx uses a network I/O credit mechanism to allocate throughput and IOPS based on average utilization — file systems accrue credits when their throughput and IOPS usage is below their baseline limits, and can use these credits when they perform I/O operations\. 

### Impact of deployment type on performance<a name="deployment-type-performance"></a>

FSx for ONTAP Multi\-AZ and Single\-AZ file systems provide consistent sub\-millisecond file operation latencies with SSD storage and tens of milliseconds of latency with capacity pool storage\. A single file system can scale up to 2 GB/s of read throughput and hundreds of thousands of IOPS\. Multi\-AZ file systems can scale up to 1 GB/s of write throughput, while Single\-AZ file systems can scale up to 750 MB/s of write throughput\. Multi\-AZ file systems also provide an NVMe read cache to reduce read latencies and increase IOPS for frequently\-read data\.

### Impact of storage capacity on performance<a name="storage-capacity-and-performance"></a>

The maximum disk throughput and IOPS levels your file system can achieve is the lower of:

Corresponding with these architectural components–network interface, in\-memory cache, NVMe cache, and storage volumes–are the primary performance characteristics of an Amazon FSx for NetApp ONTAP file system that determine the overall throughput and IOPS performance\.
+ the disk performance level provided by your file server, based on the throughput capacity you select for your file system
+ the disk performance level provided by the number of SSD IOPS you provision for your file system

By default, your file system's SSD storage provides the following levels of disk throughput and IOPS:
+ Disk throughput \(MB/s per TiB of storage\): 768
+ Disk IOPS \(IOPs per TiB of storage\): 3,072

You can optionally provision a higher level of SSD IOPS when creating your file system\.

### Impact of throughput capacity on performance<a name="impact-throughput-cap-performance"></a>

Every Amazon FSx file system has a throughput capacity that you configure when the file system is created\. The throughput capacity determines the level of network I/O performance, that is, the speed at which the file server hosting your file system can serve file data over the network to clients accessing it\. Higher levels of throughput capacity come with more memory and non\-volatile memory express \(NVMe\) storage \(for Multi\-AZ file systems\) for caching data on the file server, and higher levels of disk I/O performance supported by the file server\.

The following table shows the full set of specifications for throughput capacity, along with baseline and burst levels, and amount of memory for caching on the file server\. 


| FSx throughput capacity \(MBps\) | Network throughput capacity \(MBps\) | Network IOPS | In\-memory caching \(GB\) | NVMe caching \(GB\) \(Multi\-AZ file systems only\) | Disk throughput \(MBps\) | SSD drive IOPS \* | 
| --- |--- |--- |--- |--- |--- |--- |
|  ****  |  **Baseline**  |  **Burst**  |  ****  |  ****  |  ****  |  **Baseline**  |  **Burst**  |  **Baseline**  |  **Burst**  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |--- |
| 128 | 150 | 1,250 |  Tens of thousands baseline  | 16 | 150 | 128 | 600 | 6,000 |  18,750  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |--- |
| 256 | 300 | 1,250 | 32 | 300 | 256 | 600 | 12,000 |  18,750  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |
| 512 | 625 | 1,250 |  Hundreds of thousands baseline  | 32 | 600 | 512 | 600 | 18,750 |  –  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |--- |
| 1,024 | 1,500 |  –  | 64 | 1200 | 1,024 |   –   | 40,000 |  –  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |
| 2,048 | 3,125 |   –   | 128 | 2400 | 2,048 |   –   | 80,000 |  –  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |

**Note**  
\* Your SSD IOPS are only used when you access data that is not cached in your file server's in\-memory cache or NVMe cache\.

### Example: storage capacity and throughput capacity<a name="throughput-example-ontap"></a>

The following example illustrates how storage capacity and throughput capacity impact file system performance\. 

A file system that is configured with 2 TiB of SSD storage capacity and 512 MBps of throughput capacity has the following throughput levels:
+ Network throughput – 625 MBps baseline and 1,250 MBps burst \(see throughput capacity table\)
+ Disk throughput – 512 MBps baseline and 600 MBps burst\.

Your workload accessing the file system will therefore be able to drive up to 512 MBps baseline and 600 MBps burst throughput for file operations performed on actively accessed data cached in the file server in\-memory cache and NVMe cache\.