# Accessing CloudWatch metrics<a name="accessingmetrics"></a>

You can see Amazon CloudWatch metrics for Amazon FSx in the following ways:
+ The Amazon FSx console
+ The Amazon CloudWatch console
+ The AWS Command Line Interface \(AWS CLI\) for CloudWatch
+ The CloudWatch API

The following procedures show how to access the metrics using these various tools\.

**To view CloudWatch metrics using the Amazon FSx console**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, then choose the file system whose metrics you want to view\.

1. On the **Summary** page, choose **Monitoring** to see the metrics for your file system\. 

**To view metrics using the Amazon CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation pane, choose **Metrics**\. 

1. Select the **FSx** namespace\.

1. \(Optional\) To view a metric, enter its name in the search field\.

1. \(Optional\) To explore metrics, select the category that best matches your question: 
   + **File system metrics** and **Volume metrics** report summary\-level metrics for individual file systems or volumes\. 
   + **File system detailed metrics** and **Volume detailed metrics** report more granular metrics within a file system or volume \(for example, the storage capacity used by snapshots\)\.

**To access metrics from the AWS CLI**
+ Use the CloudWatch [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) CLI command with the `--namespace "AWS/FSx"` parameter\. For more information, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

**Using the CloudWatch API**

**To access metrics from the CloudWatch API**
+ Call the [GetMetricStatistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html) API operation\. For more information, see the [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 