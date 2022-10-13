# Mounting volumes<a name="attach-volumes"></a>

You access the data in FSx for ONTAP by mounting a volume on your client\. The commands in this section use the DNS name or the IP address of the SVM in which the volume is created to mount or attach a volume\. You can find the SVM's DNS name and IP address in the Amazon FSx console by choosing **ONTAP > Storage virtual machines**, or on the **Storage virtual machine** tab in the **File system details** page for the file system\. Or, you can find them in the response of the [DescribeStorageVirtualMachines](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeStorageVirtualMachines.html) API operation\.

**Topics**
+ [Mounting on Linux clients](attach-linux-client.md)
+ [Mounting on Microsoft Windows clients](attach-windows-client.md)
+ [Mounting on macOS clients](attach-mac-client.md)