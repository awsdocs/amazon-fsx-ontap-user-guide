# File system metrics<a name="file-system-metrics"></a>

Your Amazon FSx for NetApp ONTAP file system metrics are classified as either **File system metrics** or **Detailed file system metrics**\.
+ **File system metrics** are aggregate performance and storage metrics for a single file system that take a single dimension, `FileSystemId`\. These metrics measure network performance and storage capacity usage for your file system\.
+ **Detailed file system metrics** measure your file system's storage capacity and used storage in each storage tier \(for example, SSD storage and capacity pool storage\)\. Each metric includes a `FileSystemId`, `StorageTier`, and `DataType` dimension\.

## File system metrics<a name="fs-metrics"></a>

File system metrics are aggregate performance and storage metrics for a single file system\. These metrics measure network performance and the amount of data \(physical and logical\) stored on your file system\.

All of these metrics take one dimension, `FileSystemId`\.


| Metric | Description | 
| --- | --- | 
| DataReadBytes |  The number of bytes \(network I/O\) from reads by clients to the file system\. The `Sum` statistic is the total number of bytes associated with read operations during the period\. To calculate the average throughput \(bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Bytes Valid statistics: `Sum`  | 
| DataWriteBytes |  The number of bytes \(network I/O\) from writes by clients to the file system\. The `Sum` statistic is the total number of bytes associated with write operations during the period\. To calculate the average throughput \(bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Bytes Valid statistics: `Sum`  | 
| DataReadOperations |  The count of read operations \(network I/O\) from reads by clients to the file system\. The `Sum` statistic is the total number of I/O operations that occurred over the specified period\. To calculate the average read operations per second for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| DataWriteOperations |  The count of write operations \(network I/O\) from writes by clients to the file system\. The `Sum` statistic is the total number of I/O operations that occurred over the specified period\. To calculate the average write operations per second for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| MetadataOperations |  The count of metadata operations \(network I/O\) by clients to the file system\. The `Sum` statistic is the total number of I/O operations that occurred over the specified period\. To calculate the average metadata operations per second for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| StorageUsed |  The total amount of physical data stored on the file system, on both the primary \(SSD\) tier and the capacity pool tier\. This metric includes savings from storage\-efficiency features, such as data compression and deduplication\. Units: Bytes Valid statistics: `Average`, `Minimum`, `Maximum`  | 
| LogicalDataStored |  The total amount of logical data stored on file system, on both the primary \(SSD\) tier and the capacity pool tier\. This metric does not include storage\-efficiency savings\. To compute storage\-efficiency savings in bytes, take the `Average` of `StorageUsed` over a given period and subtract it from the `Average` of `LogicalDataStored` over the same period\.  To compute storage\-efficiency savings as a percentage of total logical data size, take the `Average` of `StorageUsed` over a given period and subtract it from the `Average` of `LogicalDataStored` over the same period\. Then divide the difference by the `Average` of `LogicalDataStored` over the same period\. Units: Bytes Valid statistics: `Average`, `Minimum`, `Maximum`  | 

## Detailed file system metrics<a name="detailed-fs-metrics"></a>

Detailed file system metrics are detailed storage\-utilization metrics for each of your storage tiers\. Detailed file system metrics all have the dimensions `FilesystemId`, `StorageTier`, and `DataType`\.
+ The `StorageTier` dimension indicates the storage tier that the metric measures, with possible values of `SSD` and `StandardCapacityPool`\.
+ The `DataType` dimension indicates the type of data that the metric measures, with the possible value `All`\.

There is a row for each unique combination of a given metric and dimensional key\-value pairs, with a description of what that combination measures\.


| Metric | Description | 
| --- | --- | 
| StorageCapacity |  The total storage capacity of the primary \(SSD\) tier\. Units: Bytes Valid statistics: `Maximum`  | 
| StorageUsed |  The used physical storage capacity in bytes, specific to the storage tier\. This value includes savings from storage\-efficiency features, such as data compression and deduplication\. Valid dimension values for `StorageTier` are `SSD` and `StandardCapacityPool`, corresponding to the storage tier that this metric measures\. This metric also requires the `DataType` dimension with the value `All`\. The `Average`, `Minimum`, and `Maximum` statistics are per\-tier storage consumption in bytes for the given period\.  To calculate storage\-capacity utilization of your primary \(SSD\) storage tier, divide any of these statistics by the `Maximum` `StorageCapacity` over the same period, with the `StorageTier` dimension equal to `SSD`\.  To calculate the free storage capacity of your primary \(SSD\) storage tier in bytes, subtract any of these statistics from the `Maximum` `StorageCapacity` over the same period, with the dimension `StorageTier` equal to `SSD`\. Units: Bytes Valid statistics: `Average`, `Minimum`, `Maximum`  | 