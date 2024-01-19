---
title: 008-Linux磁盘分区挂载与LVM
description: Linux磁盘分区挂载与LVM学习
published: true
date: 2024-01-19T09:13:08.795Z
tags: 
editor: markdown
dateCreated: 2024-01-19T09:13:08.795Z
---

## Linux磁盘分区挂载与LVM

#### 前因后果

在安装好ubuntu22.04系统后，`df -h`发现根目录挂载在`/dev/mapper/ubuntu--vg-ubuntu--lv`而且是只有100G的，（实际磁盘大小为500G）。

`fdisk -l` 查看了下磁盘，有三个分区，分别是`/dev/sda1` `/dev/sda2 `  `/dev/sda3`,

`lsblk` 查看了一下 磁盘块，挂载点的相关信息

发现上面的`ubuntu--vg-ubuntu--lv` 逻辑卷是挂在sda3下面的，占用了100G，sd3还剩下大概400G。

由于多LVM不是很了解，就这这里简单的记录学习一下。



[Linux上硬盘介绍及分区挂载学习](https://www.bilibili.com/video/BV1AW411K7x8/?spm_id_from=333.337.search-card.all.click&vd_source=80427d8d349fbb75d43d4f5000a0c454)





### 硬盘管理

MBR： master boot record  主引导记录

硬盘的0柱面 0磁头  1扇区称为主引导扇区，也叫（MBR 主引导记录）

#### 磁盘管理步骤

- 添加设备

- 分区 

  ```shell
  # 查看可用存储设备
  fdisk -l
  # 对/dev/sda 进行分区操作 按m 查看help
  fdisk /dev/sda
  ```

  

- 格式化（创建文件系统）

- 创建挂载点

  ```shell 
  # 查看已经挂载的硬盘
  df -h
  # 查看硬盘和分区的情况，包括挂载点 设备名称和大小等信息
  lsblk
  ```

  

  - 挂载 `mount /dev/sda3 /yourPoint`

- 修改配置文件



### 分区开机自动挂载

- `/etc/fstab`

  - **分区             挂载点  文件系统  属性（default） 是否备份 是否检测**
  - /dev/sd3    /xxx       xfs                   default           0              0

- 使用UUID实现开机自动挂载

  - 查看uuid:   blkid | grep sda3
  - 把上述的分区改为UUID=X1232....

- 验证：

  ```
  umount  /dev/sda3
  df -h
  mount -a
  ```

  

## LVM

[Linux LVM分区与应用与详解_](https://www.bilibili.com/video/BV16W411t7YY/?spm_id_from=333.337.search-card.all.click&vd_source=80427d8d349fbb75d43d4f5000a0c454)

LVM逻辑卷管理通过将底层物理硬盘抽象封装起来，以逻辑卷的形式表现给上层系统，逻辑卷的大小可以动态调整，而且不会丢失现有数据。新加入的硬盘也不会改变现有上层的逻辑卷。

作为一种动态磁盘管理机制，逻辑卷技术大大提高了磁盘管理的灵活性。

#### LVM的一些概念 ★

- **PE**: 物理扩展
- **PV**：物理卷
- **VG**: 卷组
- **LV**：逻辑卷
  - 取代了原本的硬盘/分区，来进行格式化挂载等操作

1. 先把磁盘格式化为物理卷PV（实际上是把磁盘分为一个个PE(默认4MB)）
2. 创建一个VG：把一些PV装进去的空间池子
3. LV：从VG中拿出部分PE组成一个LV，然后格式化后就能使用  

[Linux系统下创建LV（逻辑卷）并挂载 -腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1496311)

 **创建 VG**: 

扫面系统VG：**vgscan** 

创建VG：**vgcreate vg_test /dev/sdb1** 

查看VG：**vgdisplay** 

这样我们就创建了一个 4.98G（**1274** 个PE，要记住这个数字）的VG（名字为**vg_test**）



 **创建LV:** 

扫面系统LV：**lvscan** 

创建LV：**lvcreate -l 1274 -n lv_test vg_test** （1274是VG中PE的个数） 

查看LV：**lvdisplay** 

这样我们就创建了一个名字为 **lv_test** 的LV

```
lvcreate -l 95715 -n vic_lv ubuntu-vg
```

格式化刚刚创建的LV 命令：**mkfs -t ext4 /dev/vg_test/lv_test**

```shell
mkfs -t ext4 /dev/ubuntu-vg/vic_lv
```

 **创建目录并挂载** 

创建目录：**mkdir /test** 

挂载：**mount /dev/vg_test/lv_test /test** 

查看：**df -h** 

```
mount /dev/ubuntu-vg/vic_lv /xqd
```



我们发现系统已经挂载了刚刚创建的LV

**设置开机挂载** 

将 **/dev/mapper/vg_test-lv_test /test   ext4   defaults     1 2** 

写入 **/etc/fstab**

```
/dev/ubuntu-vg/vic_lv /xqd ext4 defaults 0 1
```





#### 补充：磁盘分类

##### 磁盘介绍与linux下磁盘管理

- **SAS硬盘** ： 只有三种容量300G 600G 900G
- **SATA硬盘**： 笔记本/台式机常用 
- **SSD硬盘**：固态硬盘
- **SCSI硬盘**： 不常见
- **IDE硬盘**：
- **磁盘管理**：

##### SAS硬盘 （服务器硬盘）

- 企业级硬盘
- SAS：串行连接SCSI接口
- SAS：serial attached SCSI，串行连接SCSI接口，串行连接小型计算机系统接口
- SAS是新一代的SCSI技术，和现在流行的Serial ATA(SATA)硬盘相同，**都是采用串行技术以获得更好的传输速度，并通过缩短连接线改善内部空间等。**
- SAS的接口技术可以向下兼容SATA。

##### SAS设计尺寸

- 3.5英寸
- 2.5英寸   节约空间，利于散热
- 

##### SSD硬盘

- 固态硬盘（solid state Drive）用固态电子存储芯片阵列制成的因公安，由控制单元和存储单元（Flash芯片、DRAM芯片）组成。
- 固态硬盘在接口的规范和定义、功能即使用方便与普通硬盘完全一致。外形尺寸等也一致
- 广泛应用于军事 车载 工控 、视频监控、网络监控、网络终端 电子 医疗 航空 导航设备等领域