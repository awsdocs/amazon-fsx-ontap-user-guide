# Using Amazon Elastic Container Service with FSx for ONTAP<a name="mount-ontap-ecs-containers"></a>

You can access your Amazon FSx for NetApp ONTAP file systems from an Amazon Elastic Container Service \(Amazon ECS\) Docker container on an Amazon EC2 Linux or Windows instance\.

## Mounting on an Amazon ECS Linux container<a name="mount-ecs-linux"></a>

1. Create an ECS cluster using the EC2 Linux \+ Networking cluster template for your Linux containers\. For more information, see [Creating a cluster](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create_cluster.html) in the *Amazon Elastic Container Service Developer Guide*\.

1. Create a directory on the EC2 instance for mounting the SVM volume as follows:

   ```
   sudo mkdir /fsxontap
   ```

1. Mount your FSx for ONTAP volume on the Linux EC2 instance by either using a user\-data script during instance launch, or by running the following commands:

   ```
   sudo mount -t nfs svm-ip-address:/vol1 /fsxontap
   ```

1. Mount the volume using the following command:

   ```
   sudo mount -t nfs -o nfsvers=NFS_version svm-dns-name:/volume-junction-path /fsxontap
   ```

   The following example uses sample values\.

   ```
   sudo mount -t nfs -o nfsvers=4.1 svm-01234567890abdef0.fs-01234567890abcdef1.fsx.us-east-1.amazonaws.com:/vol1 /fsxontap
   ```

   You can also use the SVM's IP address SVM instead of it's DNS name\.

   ```
   sudo mount -t nfs -o nfsvers=4.1 198.51.100.1:/vol1 /fsxontap
   ```

1. When creating your Amazon ECS task definitions, add the following `volumes` and `mountPoints` container properties in the JSON container definition\. Replace the `sourcePath` with the mount point and directory in your FSx for ONTAP file system\.

   ```
   {
       "volumes": [
           {
               "name": "ontap-volume",
               "host": {
                   "sourcePath": "mountpoint"
               }
           }
       ],
       "mountPoints": [
           {
               "containerPath": "containermountpoint",
               "sourceVolume": "ontap-volume"
           }
       ],
       .
       .
       .
   }
   ```

## Mounting on an Amazon ECS Windows container<a name="mount-ecs-windows"></a>

1. Create an ECS cluster using the EC2 Windows \+ Networking cluster template for your Windows containers\. For more information, see [Creating a cluster](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create_cluster.html) in the *Amazon Elastic Container Service Developer Guide*\.

1. Add a domain\-joined Windows EC2 instance to the ECS Windows cluster and map an SMB share\.

   Launch an ECS optimized Windows EC2 instance that is joined to your Active Directory domain and initialize the ECS agent by running the following command\.

   ```
   PS C:\Users\user> Initialize-ECSAgent -Cluster windows-fsx-cluster -EnableTaskIAMRole
   ```

   You can also pass the information in a script to the user\-data text field as follows\.

   ```
   <powershell>
   Initialize-ECSAgent -Cluster windows-fsx-cluster -EnableTaskIAMRole
   </powershell>
   ```

1. Create an SMB global mapping on the EC2 instance so that you can map your SMB share to a drive\. Replace the values below netbios or DNS name for your FSx file system and share name\. The NFS volume vol1 that was mounted on the Linux EC2 instance is configured as a CIFS share fsxontap on the FSx file system\.

   ```
   vserver cifs share show -vserver svm08 -share-name fsxontap
   
   
                                         Vserver: svm08
                                           Share: fsxontap
                        CIFS Server NetBIOS Name: FSXONTAPDEMO
                                            Path: /vol1
                                Share Properties: oplocks
                                                  browsable
                                                  changenotify
                                                  show-previous-versions
                              Symlink Properties: symlinks
                         File Mode Creation Mask: -
                    Directory Mode Creation Mask: -
                                   Share Comment: -
                                       Share ACL: Everyone / Full Control
                   File Attribute Cache Lifetime: -
                                     Volume Name: vol1
                                   Offline Files: manual
                   Vscan File-Operations Profile: standard
               Maximum Tree Connections on Share: 4294967295
                      UNIX Group for File Create: -
   ```

1. Create the SMB global mapping on the EC2 instance using the following command:

   ```
   New-SmbGlobalMapping -RemotePath \\fsxontapdemo.fsxontap.com\fsxontap -LocalPath Z:
   ```

1. When creating your Amazon ECS task definitions, add the following `volumes` and `mountPoints` container properties in the JSON container definition\. Replace the `sourcePath` with the mount point and directory in your FSx for ONTAP file system\.

   ```
   {
       "volumes": [
           {
               "name": "ontap-volume",
               "host": {
                   "sourcePath": "mountpoint"
               }
           }
       ],
       "mountPoints": [
           {
               "containerPath": "containermountpoint",
               "sourceVolume": "ontap-volume"
           }
       ],
       .
       .
       .
   }
   ```