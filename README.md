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
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/%E4%BC%A0%E7%BB%9F%E5%88%86%E5%8C%BA%E8%A1%A8.png width=400 height=300>
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/%E5%88%86%E5%8C%BA%E8%A1%A82.png width=400 height=300>

* 磁盘分区分为主要分区和延伸分区
* 两者加起来记录最多有4个，延伸分区最多有1个，且不可以格式化
* 逻辑分区是延伸分区继续切割出的区域

### GPT分区表

由于MBR分区表0磁区代码容量限制的原因和所能管理的磁盘容量有限，从而开发了GPT分区表技术。

<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/GPT%E5%88%86%E5%8C%BA%E8%A1%A8.png width=300 height=500>

GPT分区表储存在逻辑区块位址(LBA)中，前34个LBA用于储存相关信息，后34个LBA用于备份，理论容量可达230TB

* LBA0 主要用于兼容MBR，只是分区表换成了识别是否支持GPT分区表的程序
* LBA1 分区表及备份的位置和大小，检验机制码
* LBA2-34 主要记录区
* LBA35-64 备份区

GPT分区中的每个分区都可以看作主分区，都可以格式化

### BIOS UEFI 开机程序

### Linux安装模式下，磁盘分区的选择

Linux中的文件是以目录树的形式组织的
