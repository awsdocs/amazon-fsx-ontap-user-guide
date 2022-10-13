# Managing throughput capacity<a name="managing-throughput-capacity"></a>

FSx for ONTAP configures throughput capacity when you create the file system\. You can modify your file system's throughput capacity at any time\. Throughput capacity is one factor that determines the speed at which the file server that's hosting the file system can serve the file data\. Higher levels of throughput capacity come with higher levels of network, disk read I/O operations per second \(IOPS\), and data caching capacity on the file server\. For more information, see [Amazon FSx for NetApp ONTAP performance](performance.md)\. 

When you modify your file system's throughput capacity, Amazon FSx switches out the file server that's powering your file system\. File systems experience an automatic failover and failback during this process, which typically takes a few minutes to complete\. The failover and failback processes are transparent to NFS \(Network File Sharing\), SMB \(Server Message Block\), and iSCSI \(Internet Small Computer Systems Interface\) clients, allowing your workloads to continue running without interruption or manual intervention\. You are billed for the new amount of throughput capacity once it's available to your file system\.

**Note**  
To ensure data integrity during maintenance activity, FSx for ONTAP closes all opportunistic locks and completes any pending write operations to the underlying storage volumes that are hosting your file system before maintenance begins\. During a scheduled file system maintenance window, system modifications \(such as modifications to your throughput capacity\) may be delayed\. System maintenance can cause these changes to queue up until they are processed\. For more information, see [Optimizing performance with Amazon FSx maintenance windows](maintenance-windows.md)\.

**Topics**
+ [When to modify throughput capacity](#when-to-modify-throughput-capacity)
+ [How concurrent throughput and storage scaling requests are handled](#concurrent-throughput-and-storage-requests)
+ [How to modify throughput capacity](#increase-throughput-capacity)
+ [Monitoring throughput capacity changes](#monitoring-throughput-capacity-changes)

## When to modify throughput capacity<a name="when-to-modify-throughput-capacity"></a>

Amazon FSx integrates with Amazon CloudWatch, which helps you to monitor your file system's ongoing throughput usage levels\. The throughput and IOPS performance that you can drive through your file system depends on your specific workload’s characteristics, in addition to your file system’s throughput capacity\. As a rule, you should provision enough throughput capacity to support your workload's read throughput plus twice your workload's write throughput\. You can use CloudWatch metrics to determine which of these dimensions to change to improve performance\. For more information, see [How to use FSx for ONTAP CloudWatch metrics](monitor-throughput-cloudwatch.md)\.



## How concurrent throughput and storage scaling requests are handled<a name="concurrent-throughput-and-storage-requests"></a>

You can request a throughput capacity update just before an SSD storage capacity and provisioned IOPS update workflow begins or while it is in progress\. The sequence of how Amazon FSx handles the two requests is as follows:
+ If you submit an SSD/IOPS update and throughput capacity update at the same time, both requests will be accepted\. The SSD/IOPS update will be prioritized before the throughput capacity update\.
+ If you submit a throughput capacity update while an SSD/IOPS update is in progress, the throughput capacity update request will be accepted and queued to occur after the SSD/IOPS update\. The throughput capacity update will start after SSD/IOPS is updated \(new values are available\) and during the optimization step\. This typically takes less than 10 minutes\.
+ If you submit a SSD/IOPS update while a throughput capacity update is in progress, the SSD/IOPS storage update request will be accepted and queued to start after the throughput capacity update has completed \(new throughput capacity is available\)\. This typically takes 20 minutes\.

For more information on SSD storage and provisioned IOPS updates, see [Managing SSD storage capacity and provisioned IOPS](managing-storage-capacity.md)\.

## How to modify throughput capacity<a name="increase-throughput-capacity"></a>

You can modify a file system's throughput capacity using the Amazon FSx console, the AWS Command Line Interface \(AWS CLI\), or the Amazon FSx API\.

### To modify a file system's throughput capacity \(console\)<a name="increase-throughput-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the ONTAP file system that you want to increase the throughput capacity for\.

1. For **Actions**, choose **Update throughput capacity**\. Or, in the **Summary** panel, choose **Update** next to the file system's **Throughput capacity**\. 

   

1. Choose the new value for **Throughput capacity** from the list\.
**Note**  
You can change the throughput capacity for any FSx for ONTAP file system\. However, only file systems created on or after December 9, 2021 can support a throughput capacity of 128 MB/s or 256 MB/s\.

1. Choose **Update** to initiate the throughput capacity update\.

1. You can monitor the update progress on the **File systems** detail page, on the **Updates** tab\.

   You can monitor the progress of the update by using the Amazon FSx console, the AWS CLI, and the API\. For more information, see [Monitoring throughput capacity changes](#monitoring-throughput-capacity-changes)\.

### To modify a file system's throughput capacity \(CLI\)<a name="increase-throughput-console"></a>

To modify a file system's throughput capacity, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
+ `--file-system-id` to the ID of the file system that you are updating\.
+ `ThroughputCapacity` to the desired value to update the file system to\. 

You can monitor the progress of the update by using the Amazon FSx console, the AWS CLI, and the API\. For more information, see [Monitoring throughput capacity changes](#monitoring-throughput-capacity-changes)\.

## Monitoring throughput capacity changes<a name="monitoring-throughput-capacity-changes"></a>

You can monitor the progress of a throughput capacity modification using the Amazon FSx console, the API, and the AWS CLI\.

### Monitoring throughput capacity changes in the console<a name="monitor-throughput-action-console"></a>

On the **Updates** tab in the **File system details** window, you can view the 10 most recent update actions for each update action type\.

For throughput capacity update actions, you can view the following information\.

****Update type****  
Supported types are **Throughput capacity**, **Storage capacity**, and **Storage optimization**\.

****Target value****  
The desired value to change the file system's throughput capacity to\.

****Status****  
The current status of the update\. For throughput capacity updates, the possible values are as follows:  
+ **Pending** – Amazon FSx has received the update request, but has not started processing it\.
+ **In progress** – Amazon FSx is processing the update request\.
+ **Completed** – The throughput capacity update completed successfully\.
+ **Failed** – The throughput capacity update failed\. Choose the question mark \(**?**\) to see details on why the throughput update failed\.

****Request time****  
The time when Amazon FSx received the update request\.

### Monitoring changes with the AWS CLI and API<a name="monitor-throughput-action-cli-api"></a>

You can view and monitor file system throughput capacity modification requests using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) CLI command and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API action\. The `AdministrativeActions` array lists the 10 most recent update actions for each administrative action type\. When you modify a file system's throughput capacity, a `FILE_SYSTEM_UPDATE` administrative action is generated\. 

The following example shows the response excerpt of a `describe-file-systems` CLI command\. The file system has a throughput capacity of 128 MB/s, and a target throughput capacity of 256 MB/s\.

```
.
.
.
    "ThroughputCapacity": 128,
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1581694764.757,
        "Status": "PENDING",
        "TargetFileSystemValues": {
          "OntapConfiguration": {
            "ThroughputCapacity": 256
          }
        }
    }
]
```

When Amazon FSx processes the action successfully, the status changes to `COMPLETED`\. The new throughput capacity is then available to the file system, and shows in the `ThroughputCapacity` property\. This is shown in the following response excerpt of a describe\-file\-systems CLI command\.

```
.
.
.
    "ThroughputCapacity": 256,
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1581694764.757,
        "Status": "COMPLETED",
        "TargetFileSystemValues": {
          "OntapConfiguration": {
            "ThroughputCapacity": 256
          }
        }
    }
]
```

If the throughput capacity modification fails, the status changes to `FAILED`, and the `FailureDetails` property provides information about the failure\.