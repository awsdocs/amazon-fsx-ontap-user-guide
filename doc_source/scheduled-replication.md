# Scheduled replication using NetApp SnapMirror<a name="scheduled-replication"></a>

You can use NetApp SnapMirror to schedule periodic replication of your FSx for ONTAP file system to or from a second file system\. This capability is available for both in\-Region and cross\-Region deployments\.

NetApp SnapMirror replicates data at high speeds, so you get high data availability and fast data replication across ONTAP systems, whether you're replicating between two Amazon FSx file systems in AWS, or from on\-premises to AWS\. Replication can be scheduled as frequently as every 5 minutes, although intervals should be carefully chosen based on RPOs \(Recovery Point Objectives\), RTOs \(Recovery Time Objectives\), and performance considerations\.

When you replicate data to NetApp storage systems and continually update the secondary data, your data is kept current and remains available whenever you need it\. No external replication servers are required\. For more information about using NetApp SnapMirror to replicate your data, see [ Learn about the Replication service](https://docs.netapp.com/us-en/cloud-manager-replication/concept-replication.html) in the *NetApp Cloud Manager documentation*\.

You can use NetApp Cloud Manager or the NetApp ONTAP CLI to schedule replication for your file system\.

## Using NetApp Cloud Manager to schedule replication<a name="replication-snapmirror"></a>

You can use Cloud Manager to set up replication with SnapMirror on your FSx for ONTAP file system\. For more information, see [ Replicating data between systems](https://docs.netapp.com/us-en/cloud-manager-replication/task-replicating-data.html) in the *NetApp Cloud Manager documentation*\.

## Using the NetApp ONTAP CLI to schedule replication<a name="replication-ontap-cli"></a>

You can use the NetApp ONTAP CLI to configure scheduled volume replication\. For information, see [ Managing SnapMirror volume replication](https://docs.netapp.com/us-en/ontap/data-protection/snapmirror-replication-workflow-concept.html)\.