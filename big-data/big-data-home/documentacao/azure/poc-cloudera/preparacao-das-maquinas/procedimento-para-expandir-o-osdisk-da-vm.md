
[Documentação](../../../../documentacao.md) > [Azure](../../../azure.md) > [POC Cloudera](../../poc-cloudera.md) > [Preparacao das maquinas](../preparacao-das-maquinas.md)

# Procedimento para expandir o osDisk da VM

Resizing the partition is really simple on Azure and we can be done by the following procedure:

a) Stopping and deallocating the VM on the portal or Azure CLI / PowerShell;

b) Editing the disk on the portal and setting the size to the desired value;

c) Updating the partition information within the Linux OS which might be slightly different depending on the partitions and distribution but the concept is very similar , which is basically deleting and recreating the root partition.

NOTE: We highly recommend making a backup of the VHD prior to the steps , you can use [http://storageexplorer.com](http://storageexplorer.com/) or also [AZCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy) once the VM is deallocated

Steps to make the required partition changes are listed below per distribution and have been tested in the specific releases:

**For Red Hat 7.3 / CentOS 7.3 and Oracle 7.3**:

1) Login to the VM using SSH, we can check the size of the disk by using:  
sudo dmesg | grep -i sda

We should see something similar to the output below (assuming you changed the size to 100GB on the portal for the disk):  
[    3.100914] sd 2:0:0:0: [sda] 209715200 512-byte logical blocks: (107 GB/100 GiB)  
  
2) To proceed with the partition resize, we will use:  
sudo fdisk /dev/sda

type: p  
this will show both partitions /dev/sda1 and /dev/sda2 which are basically partitions 1 and 2

type: d then 2 (to delete partition 2)  
type: n then p , 2 (to recreate partition 2) you can accept the default values  
type: w (to save the new partition)  
type: q (to exit fdisk)  
sudo reboot (to reboot the VM so the partition is updated)

3) To finalize the resize, after the reboot, execute the command:

**For Red Hat 7.3 and CentOS 7.3**:  
sudo xfs\_growfs /dev/sda2

**For Oracle 7.3**:  
sudo btrfs filesystem resize max /

4) Use the command: df -h to verify its size:  
Filesystem      Size  Used Avail Use% Mounted on  
/dev/sda2       100G  1.6G   98G   2% /

**For Ubuntu 16.04 LTS**:

No extra steps are necessary, simply deallocating the VM, changing the size on the portal and starting the VM backup will work. You can verify the size after the VM is up by using:  
df -h  
Filesystem Size Used Avail Use% Mounted on  
/dev/sda1 97G 1.4G 96G 2% /

**For Debian 8**:

1) Login to the VM using SSH, we can check the size of the disk by using:  
sudo dmesg | grep -i sda

We should see something similar to the output below (assuming you changed the size to 100GB on the portal for the disk):  
[    3.100914] sd 2:0:0:0: [sda] 209715200 512-byte logical blocks: (107 GB/100 GiB)

2) To proceed with the partition resize, we will use:  
sudo fdisk /dev/sda

type: p  
this will show partition /dev/sda1

type: d  
type: n then p, 1 (to recreate partition 1) you can accept the default values  
NOTE: if you receive a message about deleting or keeping a signature on the disk, just answer N  
type: w (to save the new partition)  
type: q (to exit fdisk)  
sudo reboot (to reboot the VM so the partition is updated)

3) To finalize the resize, after the reboot, execute the command:  
resize2fs /dev/sda1

4) Use the command: df -h to verify its size:  
Filesystem Size Used Avail Use% Mounted on  
/dev/sda1 99G 698M 94G 1% /

**For SUSE 12 SP2 (Premium)**:

1) Login to the VM using SSH, we can check the size of the disk by using:  
sudo dmesg | grep -i sda

We should see something similar to the output below (assuming you changed the size to 100GB on the portal for the disk):  
[    3.100914] sd 2:0:0:0: [sda] 209715200 512-byte logical blocks: (107 GB/100 GiB)

2) To proceed with the partition resize, we will use:  
sudo fdisk /dev/sda

type: p  
this will show both partitions /dev/sda1 and /dev/sda2 which are basically partitions 1 and 2

type: d then 2 (to delete partition 2)  
type: n then p , 2 (to recreate partition 2) you can accept the default values  
type: w (to save the new partition)  
type: q (to exit fdisk)  
sudo reboot (to reboot the VM so the partition is updated)

3) To finalize the resize, after the reboot, execute the command:  
resize2fs /dev/sda2

4) Use the command: df -h to verify its size:  
Filesystem Size Used Avail Use% Mounted on  
/dev/sda2 98G 1.2G 92G 2% /

Fonte: <https://blogs.msdn.microsoft.com/linuxonazure/2017/04/03/how-to-resize-linux-osdisk-partition-on-azure/>
