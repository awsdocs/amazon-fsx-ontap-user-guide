# Volume metrics<a name="volume-metrics"></a>

Your Amazon FSx for NetApp ONTAP file system can have one or more volumes that store your data\. Each of these volumes has a set of metrics, classified as either **Volume metrics** or **Detailed volume metrics**\.
+ **Volume metrics** are per\-volume performance and storage metrics that take two dimensions, `FileSystemId` and `VolumeId`\. `FileSystemId` maps to the file system that the volume belongs to\.
+ **Detailed volume metrics** are per\-storage\-tier metrics that measure storage consumption per tier with the `StorageTier` dimension \(with possible values of `SSD` and `StandardCapacityPool`\) and per data type with the `DataType` dimension \(with possible values of `User`, `Snapshot`, and `Other`\)\. These metrics have the `FileSystemId`, `VolumeId`, `StorageTier`, and `DataType` dimensions\.

## Volume metrics<a name="vol-metrics"></a>

Volume metrics are high\-level metrics measured per volume that give you insight into the throughput, IOPS, and average latency of each volume, as well as the storage capacity of each volume\.

All of these metrics take two dimensions, `FileSystemId` and `VolumeId`\.


| Metric | Description | 
| --- | --- | 
| DataReadBytes |  The number of bytes \(network I/O\) read from the volume by clients\. The `Sum` statistic is the total number of bytes associated with read operations during the period\. To calculate the average throughput \(bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Bytes Valid statistics: `Sum`  | 
| DataWriteBytes |  The number of bytes \(network I/O\) written to the volume by clients\. The `Sum` statistic is the total number of bytes associated with write operations during the period\. To calculate the average throughput \(bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Bytes Valid statistics: `Sum`  | 
| DataReadOperations |  The number of read operations \(network I/O\) on the volume by clients\. The `Sum` statistic is the total number of read operations during the period\. To calculate the average read operations per second for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| DataWriteOperations |  The number of write operations \(network I/O\) on the volume by clients\. The `Sum` statistic is the total number of write operations during the period\. To calculate the average write operations per second for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| MetadataOperations |  The number of I/O operations \(network I/O\) from metadata activities by clients to the volume\. The `Sum` statistic is the total number of metadata operations during the period\. To calculate the average metadata operations per second for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| DataReadOperationTime |  The sum of total time spent within the file system for read operations \(network I/O\) from clients accessing data in the volume\. The `Sum` statistic is the total number of seconds spent by read operations during the period\. To calculate the average read latency for a period, divide the `Sum` statistic by the `Sum` of the `DataReadOperations` metric over the same period\. Units: Seconds Valid statistics: `Sum`  | 
| DataWriteOperationTime |  The sum of total time spent within the file system for fulfilling write operations \(network I/O\) from clients accessing data in the volume\. The `Sum` statistic is the total number of seconds spent by write operations during the period\. To calculate the average write latency for a period, divide the `Sum` statistic by the `Sum` of the `DataWriteOperations` metric over the same period\. Units: Seconds Valid statistics: `Sum`  | 
| MetadataOperationTime |  The sum of total time spent within the file system for fulfilling metadata operations \(network I/O\) from clients that are accessing data in the volume\. The `Sum` statistic is the total number of seconds spent by read operations during the period\. To calculate the average latency for a period, divide the `Sum` statistic by the `Sum` of the `DataReadOperations` over the same period\. Units: Seconds Valid statistics: `Sum`  | 
| StorageCapacity |  The size of the volume in bytes\. Units: Bytes Valid statistics: `Maximum`  | 
| StorageUsed |  The used physical storage capacity of the volume\. This metric includes data savings from storage\-efficiency features, such as compression and deduplication\. Units: Bytes Valid statistics: `Average`, `Minimum`, `Maximum`  | 
| FilesUsed |  The used files \(number of files or inodes\) on the volume\. Units: Count Valid statistics: `Average`, `Minimum`, `Maximum`  | 
| FilesCapacity |  The total number of inodes that can be created on the volume\. Units: Count Valid statistics: `Maximum`  | 

## Detailed volume metrics<a name="detailed-vol-metrics"></a>

Detailed volume metrics take more dimensions than volume metrics, enabling more granular measurements of your data\. All detailed volume metrics have the dimensions `FileSystemId`, `VolumeId`, `StorageTier`, and `DataType`\.
+ The `StorageTier` dimension indicates the storage tier that the metric measures, with possible values of `All`, `SSD`, and `StandardCapacityPool`\.
+ The `DataType` dimension indicates the type of data that the metric measures, with possible values of `All`, `User`, `Snapshot`, and `Other`\.

There is a row for each unique combination of a given metric and dimensional key\-value pairs, with a description of what that combination measures\.

**Note**  
Detailed volume metrics are not available for FlexGroup volumes\.


| Metric | Description | 
| --- | --- | 
| StorageUsed |  The amount of physical space \(includes savings from storage efficiency\) that this volume consumes per storage tier \(footprint\)\. The valid dimension values for `StorageTier` are `SSD` and `StandardCapacityPool`, corresponding to the storage tier that this metric measures\. The footprint includes any overheads in either tier, including reserves or space guarantees that are configured on the volume\. This metric also requires the `DataType` dimension with the value `All`\. Units: Bytes Valid statistics: `Average`, `Minimum`, `Maximum`  | 
| StorageUsed |  The amount of physical data \(includes savings from storage efficiency\) on this volume that is attributed to a given data type, indicated by the possible `DataType` dimension values of `User`, `Snapshot`, and `Other`\. `Snapshot` includes snapshot reserves, and `Other` corresponds to volume and file metadata\. This metric also requires the `StorageTier` dimension with the value `All`\. Units: Bytes Valid statistics: `Average`, `Minimum`, `Maximum`  | 