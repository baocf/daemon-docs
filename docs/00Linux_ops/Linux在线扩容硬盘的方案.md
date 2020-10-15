### Linux在线扩容硬盘的方案



#### 1. 获取Vsphere Vitual Server Disk Bind Relate

### 方式1：

​	查看硬盘信息

```shell
[root@node102030 ~]$yum install lshw
[root@node102030 ~]$lshw -C disk
  *-disk:0                  
       description: SCSI Disk
       product: Virtual disk
       vendor: VMware
       physical id: 0.0.0
       bus info: scsi@32:0.0.0
       logical name: /dev/sda
       version: 1.0
       size: 50GiB (53GB)
       capabilities: 7200rpm partitioned partitioned:dos
       configuration: ansiversion=2 logicalsectorsize=512 sectorsize=512 signature=000ea564
  *-disk:1
       description: SCSI Disk
       product: Virtual disk
       vendor: VMware
       physical id: 0.1.0
       bus info: scsi@32:0.1.0
       logical name: /dev/sdb
       version: 1.0
       size: 500GiB (536GB)
       capabilities: 7200rpm partitioned partitioned:dos
       configuration: ansiversion=2 logicalsectorsize=512 sectorsize=512 signature=c7a17e60
  *-disk:2
       description: SCSI Disk
       product: Virtual disk
       vendor: VMware
       physical id: 0.2.0
       bus info: scsi@32:0.2.0
       logical name: /dev/sdc
       version: 1.0
       size: 200GiB (214GB)
       capabilities: 7200rpm partitioned partitioned:dos
       configuration: ansiversion=2 logicalsectorsize=512 sectorsize=512 signature=0b47237b
  *-disk:3
       description: SCSI Disk
       product: Virtual disk
       vendor: VMware
       physical id: 0.3.0
       bus info: scsi@32:0.3.0
       logical name: /dev/sdd
       version: 1.0
       size: 400GiB (429GB)
       capabilities: 7200rpm partitioned partitioned:dos
       configuration: ansiversion=2 logicalsectorsize=512 sectorsize=512 signature=0019f502
```

### 方式2: lsscsi

```shell
[root@node102030 ~]$ lsscsi 
[2:0:0:0]    cd/dvd  NECVMWar VMware SATA CD00 1.00  /dev/sr0 
[32:0:0:0]   disk    VMware   Virtual disk     1.0   /dev/sda 
[32:0:1:0]   disk    VMware   Virtual disk     1.0   /dev/sdb 
[32:0:2:0]   disk    VMware   Virtual disk     1.0   /dev/sdc 
[32:0:3:0]   disk    VMware   Virtual disk     1.0   /dev/sdd 
```





#### 2、先停止相关业务，并做好备份

#### 3、umount /dev/{sdb1-sdbn} 卸载sdb的所有分区

#### 4、root用户执行partprobe命令，让Linux系统识别到扩容的磁盘容量,第二次卸载umount /dev/{sdb1-sdbn}

#### 5、fdisk /dev/sdb 删除原分区，重新建分区，第三次卸载umount /dev/{sdb1-sdbn}

#### 6、xfs_repair /dev/sdb1  修复分区

#### 7、mount /dev/sdb1 /data  挂载分区

#### 8、xfs_growfs /dev/sdb1  扩容分区