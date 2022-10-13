# Monitoring Amazon FSx for NetApp ONTAP<a name="monitoring_overview"></a>

You can use the following services and tools to monitor Amazon FSx for NetApp ONTAP usage and activity:
+ **Amazon CloudWatch** – You can monitor file systems using Amazon CloudWatch, which automatically collects and processes raw data from FSx for ONTAP into readable metrics\. These statistics are retained for a period of 15 months so that you can access historical information and see how your file system is performing\. You can also set alarms based on your metrics over a specified time period and perform one or more actions based on the value of the metrics relative to thresholds that you specify\.
+ **NetApp Cloud Insights** – You can monitor configuration, capacity, and performance metrics for your FSx for ONTAP file systems using the NetApp Cloud Insights service\. You can also create alerts based on metric conditions\.
+ **NetApp Harvest and NetApp Grafana** – You can monitor your FSx for ONTAP file system by using NetApp Harvest and NetApp Grafana\. NetApp Harvest monitors ONTAP file systems by collecting performance, capacity, and hardware metrics from FSx for ONTAP file systems\. Grafana provides a dashboard where the collected Harvest metrics can be displayed\.
+ **AWS CloudTrail** – You can use AWS CloudTrail to capture all API calls for Amazon FSx as events\. These events provide a record of actions taken by a user, role, or AWS service in Amazon FSx\.

**Topics**
+ [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)
+ [Monitoring with Cloud Insights](monitoring-cloud-insights.md)
+ [Monitoring FSx for ONTAP file systems using Harvest and Grafana](monitoring-harvest-grafana.md)
+ [Logging FSx for ONTAP API Calls with AWS CloudTrail](logging-using-cloudtrail-win.md)