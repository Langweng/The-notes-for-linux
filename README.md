# The-notes-for-linux
小菜鸡因为最近在入坑深度学习，所以正在学习Linux相关的知识，把这里当作记笔记的地方

## 计算机中的各个硬件在linux中的文件名

|硬件名|在linux系统中的名称|
|:---             |:---              |
|SCSI/SATA/USB硬盘/USB闪存盘|/dev/sd[a-p] |     
|Virtl/O界面（虚拟机）|/dev/vd[a-p]|
|软盘|/dev/fd[0-7]|
|打印机|/dev/usb/lp[0-15]  or  /dev/lp[0-2]|
|鼠标|/dev/input/mouse[0-15]（通用）  or  /dev/mouse（当前鼠标） or /dev/psaux(PS/2界面)|
|CDROM/DVDROM|/dev/scd[0-1]|
|磁带机|/dev/ht[0] (IDE界面) /dev/st[0] (SATA/SCSI界面) /dev/tape (当前磁带) |

## 磁盘分区

不同接口的顺序：SATA>USB 按字母表顺序，与实际所插位置无关

### MBR分区表

MBR分区是早期linux系统为了兼容windows系统而规定的磁盘分区格式，它把磁盘分为了主要分区和延伸分区。
MBR分区的关键之处在于磁盘的第一个分区，该分区被分为两个部分，第一个即为主要开机记录区，负责储存相关的开机程序，
占446Bytes，而分区表占64Bytes。

基本的MBR分区表是把磁盘分为4各部分，当然也可以只规定主要分区和延伸分区，从而对延伸分区进行再划分
