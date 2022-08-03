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

<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/%E7%9B%AE%E5%BD%95%E6%A0%91.png width=400 height=300>

## CentOS 7指令入门

### 查询指令

-- help 查询有关指令的帮助

按两次[Tab]补全指令

man page模式

其中man page模式介绍最为详细，按q退出，1代表可执行程序，5代表文件，8代表管理员可用的管理指令

有时候会用man -f [] 搜寻特定指令，man -k [] 则是搜寻关键字，分别相当于whatis apropos

此外寻求指令帮助还可以用info指令阅读超链接文本

## 文件权限与目录配置

 Linux中文件可存取的身份有owner/group/others三种，且三种身份各有read/write/execute三种权限
 
 文件属性分为7栏，分别为文件的类型和权限、文件的链接节点、文件的拥有者、文件所属的群组、文件的大小、文件的创建或修改日期、文件的名称。
 
 其中文件的类型主要有：
 
 * [d]目录文件
 * [-]文件
 * [l]链接文件
 * [b]可供储存的周边设备
 * [c]序列埠设备

### 修改文件权限

* chgrp可以修改文件所属的群组，语法为chgrp [-R] [群组名] [文件名] 其中[-R]是可选参数，代表修改连同该目录下的所有文件
* chown可以修改所有者和群组，语法为chown [-R] [所有者：群组名] [文件名]
* chmod可以修改三个主体对于文件的权限，修改模式有数字型修改和字母型修改

数字型修改：采用二进制的原理每个类型的主题有一个数字，对应唯一的权限：r-4 w-2 x-1 --0
* 语法：chmod [-R] 数字 文件名

字母型修改：每种主体的的代号：user-u group-g other-o all-a  
* 语法：chmod |u,g,o,a| +/-/=|rwx|文件或目录|

要读取一个文件通常需要这个文件所属目录下的x权限才行，所以目录一般对外开放rx权限，另外，即使没有文件的w权限，只要有目录的w权限即可删除目录下的文件。

### Linux目录配置
FHS标准主要通过四种文件类型两两交叉分类，它们分别为：可分享的，不可分享的，可变的，不变的

FHS定义了三层目录应该放置什么数据：
* /(root根目录)
* usr(user software resources 与软件安装/执行有关)
* var(variable 与系统安装、执行有关)

### 绝对路径和相对路径
* 从根目录写起的叫绝对路径
* 从当前目录写起的叫相对路径，其中.代表当前目录，..代表当前目录的上级目录

## Linux 文件与目录管理
### 目录的相关操作
特殊目录

|符号|意义|
|:---|:---|
|.|当前目录|
|..|当前目录的上一级目录|
|-|前一个目录|
|~|当前使用者账号所在的主目录|
|~account|账号account所在的主目录|

处理目录的指令
* cd 变换目录
cd [目录]
* pwd 显示当前目录
pwd [-P] -P是可选参数，表示不以链接形式显示，直接显示本源文件目录
* mkdir 创建目录
mkdir [-mp] [目录名] -m:设置权限(-m 711)-p:创建多层目录(test1/test2/test3)
* rmdir 删除“空”的目录
rmdir [-p] [目录名] -p:与mkdir的-p作用一致

### 可执行文件路径的变量：$PATH
当在命令行中输入可执行文件的名称之后，系统会自动从PATH的路径中进行搜寻
* echo $PATH 显示路径
* PATH="${PATH}:[目录名]" 添加路径

### 文件与目录管理

* 文件与目录的检视经常用到ls命令
* cp:复制文件或目录
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/mv%E5%91%BD%E4%BB%A4.png width=700 height=250>
* rm:删除文件
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/rm%E5%91%BD%E4%BB%A4.png width=700 height=200>
* mv:移动文件
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/mv%E5%91%BD%E4%BB%A4.png width=700 height=250>

文件名和目录名常用命令basename dirname 来截取

### 文件内容查阅

* cat 从第一行显示文件的内容
* tac 从最后一行显示文件的内容
* nl 顺便显示文件的行号
* more 一页一页地显示文件的内容
* less 一页一页地显示，并且可以向前翻页
* head 只看头几行
* tail 只看最后几行
* od 以二进制的形式读取文件的内容

### 修改文件时间或创建新文件
文件的时间属性主要有3个，分别是：
*　modification time(mtime)文件数据内容变更时间
*　status time(ctime)文件的状态权限变更时间
*　access time(atime)文件的内容被取用的时间

### 文件的隐藏属性
umask:创建文件时文件的默认权限

umask为数字显示形式，为各个权限需要减去的数字 umask -S则显示代码格式

文件的隐藏属性使用lsattr显示，使用chattr进行设置两者设置的格式如下：
* chattr
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/%E8%AE%BE%E7%BD%AE%E5%B1%9E%E6%80%A7.png>
* lsattr
<img src=https://github.com/Langweng/The-notes-for-linux/blob/main/%E6%9F%A5%E7%9C%8B%E5%B1%9E%E6%80%A7.png>

### 文件的特殊权限
* SUID
该权限只出现在文件原本应出现x权限的位置上，执行过程中会暂时获得文件owner的权限
* SGID
同理，执行过程中会暂时获得文件group的权限
* SBIT
仅对目录有效，当使用者有目录的w和x权限时，加之SBIT则只有创建者和root能够删除文件

设置特殊权限同理，只需要把数字变为四位，SUID/SGID/SBIT赋值分别为4/2/1
