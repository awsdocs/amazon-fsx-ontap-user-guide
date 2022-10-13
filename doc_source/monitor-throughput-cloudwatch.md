# How to use FSx for ONTAP CloudWatch metrics<a name="monitor-throughput-cloudwatch"></a>

The metrics reported by Amazon FSx provide information that you can analyze in different ways\. The list following shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


| How do I determine\.\.\. | Relevant metrics | 
| --- | --- | 
|  How many IOPS my workloads are driving  |  SUM\(DataReadOperations \+ DataWriteOperations \+ MetadataOperations\)/Period \(in seconds\)  | 
|  How much throughput my workloads are driving  | SUM\(DataReadBytes \+ DataWriteBytes\)/Period \(in seconds\) | 
|  The utilization of my file system’s throughput capacity  |  SUM\(DataReadBytes \+ 2\*DataWriteBytes\)/Period \(in seconds\)/1024/1024/<File system’s throughput capacity in MBps>  | 

**Note**  
We recommend that you maintain an average throughput capacity utilization under 50% to ensure that you have enough spare throughput capacity for unexpected spikes in your workload, as well as for any background storage operations \(such as storage synchronization, data tiering, or backups\)\.