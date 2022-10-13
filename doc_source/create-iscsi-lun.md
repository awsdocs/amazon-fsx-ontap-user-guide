# Creating an iSCSI LUN<a name="create-iscsi-lun"></a>

This process describes how to create an iSCSI LUN on an Amazon FSx for NetApp ONTAP file system using the NetApp ONTAP CLI\. 

**Note**  
We recommend that you use a volume at least 5% larger than your LUN size\. This margin leaves space for volume snapshots\.

This process assumes you already have a volume created on your file system\. For more information, see [Creating a volume](managing-volumes.md#creating-volumes)\.

1. Use SSH to connect to your FSx for ONTAP file system using the NetApp ONTAP CLI\. For more information, see [Using the NetApp ONTAP CLI](managing-resources-ontap-apps.md#netapp-ontap-cli)\.

   ```
   [~]$ ssh fsxadmin@management_endpoint_ip
   ```

1. Create a LUN, specifying the following properties: 
   + **`svm_name`** \- The name of the storage virtual machine providing the iSCSI target\. The host uses this value to reach the LUN\.
   + **`vol_name`** \- The name of the volume hosting the LUN\.
   + **`lun_name`** \- The name you want to assign to the LUN\.
   + **`size`** \- The size, in bytes, of the LUN\.
   + **`ostype`** \- The operating system of the host, either `windows_2008` or `linux`\. Use `windows_2008` for all versions of Windows; this ensures the LUN has proper block offset for the operating system and optimizes performance\.
**Note**  
We recommend enabling space allocation on your LUN\. With space allocation enabled, ONTAP can inform your host when the LUN is out of capacity and can reclaim space as you delete data from the LUN\. 

   ```
   > lun create -vserver svm_name -path /vol/vol_name/lun_name -size size -ostype ostype -space-allocation enabled
   ```

   ```
   Created a LUN of size 10g (10737418240)
   ```

   For more information, see [lun create](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-cmpr-9101%2Flun__create.html) in the NetApp Documentation Center\.

1. Confirm the LUN is created, online, and mapped\.

   ```
   > lun show
   ```

   The system responds with the following output:

   ```
   Vserver   Path                            State   Mapped   Type        Size
   --------- ------------------------------- ------- -------- -------- --------
   svm_name 
             /vol/vol_name/lun_name      online  unmapped windows      10GB
   ```

## Next steps<a name="iscsi-next-steps"></a>

Now that you have created an iSCSI LUN, the next step in the process of using an iSCSI LUN as block storage is to map the LUN to an `igroup`\. For more information, see [Mounting iSCSI LUNs to a Linux client](mount-iscsi-luns-linux.md) or [Mounting iSCSI LUNs to a Windows client](mount-iscsi-windows.md)\.