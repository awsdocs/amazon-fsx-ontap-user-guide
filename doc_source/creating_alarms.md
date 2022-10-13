# Creating Amazon CloudWatch alarms to monitor Amazon FSx<a name="creating_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon Simple Notification Service \(Amazon SNS\) message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. If needed, the alarm then performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or an Auto Scaling policy\.

Alarms invoke actions for sustained state changes only\. CloudWatch alarms don't invoke actions only because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. You can create an alarm from the Amazon FSx console or the Amazon CloudWatch console\.

The following procedures describe how to create alarms for Amazon FSx using the console, AWS Command Line Interface \(AWS CLI\), and API\.

**To set alarms using the Amazon FSx console**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the left navigation pane, choose **File systems**, and then choose the file system that you want to create the alarm for\.

1. On the **Summary** page, choose **Monitoring**\. 

1. Choose **Create CloudWatch alarm**\. You are redirected to the CloudWatch console\.

1. Choose **Select metric**\.

1. In the **Metrics** section, choose **FSX**\.

1. Choose a metric category:
   + **File System Metrics**
   + **Detailed File System Metrics**
   + **Volume Metrics**
   + **Detailed Volume Metrics**

1. Choose the metric you want to set the alarm for, and then choose **Select metric**\.

1. In the **Conditions** section, choose the conditions you want for the alarm, and choose **Next**\.
**Note**  
Metrics may not be published during file system maintenance\. To prevent unnecessary and misleading alarm condition changes and to configure your alarms so that they are resilient to missing data points, see [Configuring how CloudWatch alarms treat missing data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#alarms-and-missing-data) in the *Amazon CloudWatch User Guide*\.

1. If you want CloudWatch to send you an email or Amazon SNS notification when the alarm state initiates the action, choose an alarm state for **Whenever this alarm state is**\. 

   For **Select an SNS topic**, choose an existing Amazon SNS topic\. If you choose **Create topic**, you can set the name and email addresses for a new email subscription list\. This list is saved and appears in the field for future alarms\. Choose **Next**\.
**Note**  
If you use **Create topic** to create a new Amazon SNS topic, the email addresses must be verified before they receive notifications\. Emails are sent only when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, they do not receive a notification\.

1. Fill in the **Name**, **Description**, and **Whenever** values for the metric, and choose **Next**\. 

1. On the **Preview and create** page, review the alarm that you're about to create, and then choose **Create Alarm**\. 

**To set alarms using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Create Alarm** to start the **Create Alarm Wizard**\. 

1. Follow the procedure in **To set alarms using the Amazon FSx console**, beginning with step 5\.

**To set an alarm using the AWS CLI**
+ Call the [put\-metric\-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html) CLI command\. For more information, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

**To set an alarm using the CloudWatch API**
+ Call the [PutMetricAlarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html) API operation\. For more information, see the [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 