##  一、 VMware简介：
VMware是一个虚拟PC的软件，可以在现有的操纵系统上虚拟出一个新的硬件环境，相当于模拟出一台新的PC，以此来实现在一台机器上真正同时运行两个独立的操作系统。
VMware的主要特点：
不需要区分或重新开机就能在同一台PC上使用两种以上的操作系统；
本机系统可以与虚拟机系统网络通信；
可以设定并且随时修改虚拟机操作系统的硬件环境。

##  二、CentOS简介：
CentOS（Community ENTerprise Operating System）是Linux发行版之一，它是来自于Red Hat Enterprise Linux依照开放源代码规定释出的源代码所编译而成。由于出自同样的源代码，因此有些要求高度稳定性的服务器以CentOS替代商业版的Red Hat Enterprise Linux使用。两者的不同，在于CentOS并不包含封闭源代码软件。

##  三、CentOS的安装：
虚拟机版本：VMware Workstation_10.0.3
Linux系统版本：CentOS_7（64位）
物理机版本：Win 7旗舰版
注：本人为初学者，在虚拟机上安装CentOS，方便同时在Win7上操作以及和物理机进行通信；等操作熟练之后可以装双系统。
###1、虚拟机的创建：
打开安装好的VMware （如果大家安装的VMware是英文版的也没关系，对号入座即可），选择“文件--->新建虚拟机”，如图：
![image](https://github.com/billxq/notes/blob/master/images/171110533588371.png)

这里选择“自定义”，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171111068424925.png)

选择默认，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171111238896866.png)

选择“第三项“稍后安装操作系统”，以后安装，方便进行配置。下一步：
（注：上图中，第一项是用光盘安装，第二项是用硬盘里的ISO镜像安装）
![image](https://github.com/billxq/notes/blob/master/images/171112106393415.png)

上图指的是，打算在虚拟机中模拟一个怎样的操作系统，这里选择Linux，Version里选择CentOS 64位，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171112221868409.png)

输入虚拟机名称（可以随便起），选择镜像文件存放的路径。下一步：
![image](https://github.com/billxq/notes/blob/master/images/171112437023882.png)

选择给虚拟机分配的cpu核的数量，如果为虚拟机选择的cpu数超过本机实际cpu数量，VMware会给出警告。这里选择默认即可。下一步：
![image](https://github.com/billxq/notes/blob/master/images/171112553423606.png)

虚拟内存分配，一般选默认的1024MB就行（注：给虚拟机分配的内存不能超过物理 内存的一半，而且稍后如果想要使用图形界面安装CentOS，至少要分配628M的内存），下一步：
![image](https://github.com/billxq/notes/blob/master/images/171113191709452.png)

这里是网络类型分配，网络类型分配分为三种，Bridge，NAT和Host-Only，大概区别是
* 1 BRIDGE:相当于主机和虚拟机连接到同一个hub上，虚拟机要和主机配置到同一网段上

* 2 NAT:使用vmnet8，主机开启必要的VMware服务，如vmvare dhcp，虚拟机设置成dhcp方式即可，当然也可以手工设置成vmnet1同一网段，比较麻
* 3 HOST-ONLY：使用vmnet1，直接和主机互联，可以使用ifconfig查看配置情况
这里选择NAT，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171113379835965.png)

选择io控制器类型，选择默认的即可，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171114028274755.png)

选择要创建的磁盘类型，选择默认的即可，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171114167644380.png)

选择虚拟硬盘类型，这里选择创建一个新的，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171114417955928.png)
选择磁盘空间大小，默认即可，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171115107179663.png)

设置虚拟镜像文件名称，默认即可，下一步：
![image](https://github.com/billxq/notes/blob/master/images/171115211236443.png)

创建完成，确认虚拟机信息（其实以后也可以修改），点“完成”即可。出现了下面的界面：
![image](https://github.com/billxq/notes/blob/master/images/171115385772741.png)

这就是我们虚拟出来的一台计算机，上方红框中，可以看到虚拟机的硬件信息，也可以进行修改，或者点击菜单栏“虚拟机--->设置”，也可以对硬件配置进行修改。
虚拟机的使用技巧：【创建快照】（可忽略）
快照的功能相当于一件还原，当我们在后续操作中，即使将虚拟机搞崩溃了，读取快照，就可以还原到此时的状态。
![image](https://github.com/billxq/notes/blob/master/images/171116170147584.png)

点击上方红框中的按钮，或者选择菜单栏“虚拟机--->”快照：
![image](https://github.com/billxq/notes/blob/master/images/171116307952695.png)

给快照起个名字。以后如果想要恢复快照，点击下方红框中的按钮：
![image](https://github.com/billxq/notes/blob/master/images/171116449057904.png)

弹出对话框：
![image](https://github.com/billxq/notes/blob/master/images/171116545304811.png)

选择之前保存的快照，点击“转到”，即可。
注：快照会占用一定的内存，而且，真正的服务器是没有快照的功能。
### 2、安装CentOS镜像：
首先需要加载CentOS镜像。选择“虚拟机--->设置”：
![image](https://github.com/billxq/notes/blob/master/images/171117558899357.png)
点击”CD/DVD(IDE)”,打开虚拟机光盘设置页面，点击”使用ISO镜像文件”,选择待安装的ISO镜像文件。就是刚刚下载的CentOS-7.0-1406-x86_64-DVD.iso文件。点击“确定”：
![image](https://github.com/billxq/notes/blob/master/images/171118164523672.png)

然后点击”开启此虚拟机“，启动虚拟机。
PS:
打开虚拟机之后，提示了一个小错误，错误如下：
![image](https://github.com/billxq/notes/blob/master/images/171119019363735.png)
根据错误提示，到BIOS里面设置了一下，这里把Intel virtual Tehnology 的设置改为enabled 就可以啦：
![image](https://github.com/billxq/notes/blob/master/images/171120006868350.jpg.jpeg)

然后重启继续安装。这个时候，要赶紧将鼠标点进去，并快速按F2键，弹出如下的BIOS启动界面：
（注：解释如下，计算机默认是从硬盘启动的，但是这个虚拟机现在是空的，所以现在要设置为通过光盘启动，才能进入安装界面）
![image](https://github.com/billxq/notes/blob/master/images/171120312807629.png)

根据上图所示，选择“Boot”菜单，然后通过键盘上的“+”将“CD-ROM Drive”放在最上面，即优先启动。
![image](https://github.com/billxq/notes/blob/master/images/171120480145243.png)

紧接着，按照上图所示，进入Exit菜单，选择第二项“Exit Discarding Changes”，即不保存退出，按enter键确认。
之后出现如下界面：
![image](https://github.com/billxq/notes/blob/master/images/171121087808086.png)

在上图中，选择第一项“Install CentOS 7”，按enter键确认。之后，弹出如下图形化的安装界面：
![image](https://github.com/billxq/notes/blob/master/images/171121397332410.png)

选择简体中文的语言，单击“继续”，弹出如下界面：
![image](https://github.com/billxq/notes/blob/master/images/171121591707982.png)

日期和时间：
![image](https://github.com/billxq/notes/blob/master/images/171122133278205.png)
![image](https://github.com/billxq/notes/blob/master/images/171122248738901.png)

如果你安装的是英文版，需要将时区改为上海。本人安装的是中文版，选择默认的上海时间即可。
键盘：
![image](https://github.com/billxq/notes/blob/master/images/171122543733980.png)
![image](https://github.com/billxq/notes/blob/master/images/171123082335647.png)

英文键盘和中文键盘布局是一样的，默认即可。
安装源：
![image](https://github.com/billxq/notes/blob/master/images/171123388583170.png)
![image](https://github.com/billxq/notes/blob/master/images/171123512484523.png)

可以看到，安装源是自动检测的，不用修改，单击“完成”即可。
软件选择：
![image](https://github.com/billxq/notes/blob/master/images/171552324983655.png)
![image](https://github.com/billxq/notes/blob/master/images/171552448899306.png)

个人用户建议选择桌面版，服务器建议选择最小安装。我这里选择桌面版，并根据需要安装一些软件及工具（反正我是全安装了的）。
网络和主机名：
![image](https://github.com/billxq/notes/blob/master/images/171553210617790.png)
![image](https://github.com/billxq/notes/blob/master/images/171553297807553.png)

打开网络连接就行了，单击“完成”。
安装位置：
![image](https://github.com/billxq/notes/blob/master/images/171553481552468.png)
![image](https://github.com/billxq/notes/blob/master/images/171554090305811.png)

选择“我要配置分区“，点左上角的“完成”，进入下面的界面：
![image](https://github.com/billxq/notes/blob/master/images/171556151234235.png)

根据上图所示，分区方案选择“标准分区”。并开始挂载分区：
将/boot分区设为200M：
![image](https://github.com/billxq/notes/blob/master/images/171557342333636.png)
![image](https://github.com/billxq/notes/blob/master/images/171557516399620.png)
![image](https://github.com/billxq/notes/blob/master/images/171557516399620.png)

将swap分区设为2G：（这里不用修改文件系统方式，默认的方式为swap即可）
![image](https://github.com/billxq/notes/blob/master/images/171558113426507.png)

将/home目录设置为2G：
![image](https://github.com/billxq/notes/blob/master/images/171558414675943.png)
![image](https://github.com/billxq/notes/blob/master/images/171602339051487.png)

最后，给根分区/分配剩余的全部空间：
![image](https://github.com/billxq/notes/blob/master/images/171602431234038.png)
![image](https://github.com/billxq/notes/blob/master/images/171603562021163.png)

分配完了，单击左上角的“完成”，出现如下界面：
![image](https://github.com/billxq/notes/blob/master/images/171604227644211.png)

接受更改就行了。
配置完成了，单击“开始安装“：
![image](https://github.com/billxq/notes/blob/master/images/171604420612040.png)

在安装的过程中，我们可以设置一下root密码，还可以创建一个常用账号：
![image](https://github.com/billxq/notes/blob/master/images/171604599524638.png)
这样，就大功告成了。

##  *关于centos7的分区：*
CentOS分区方案 
* （ 1 ）最基本分区方案：
/ ：建议大小在 5GB 以上。 
swap ：即交换分区，建议大小是物理内存的 1~2 倍。
* （ 2 ）进阶分区方案：
/boot ：用来存放与 Linux 系统启动有关的程序，比如启动引导装载程序等，建议大小为 100MB 。 
/ ： Linux 系统的根目录，所有的目录都挂在这个目录下面，建议大小为 5GB 以上。 
/home ：存放普通用户的数据，是普通用户的宿主目录，建议大小为剩下的空间。 
swap ：实现虚拟内存，建议大小是物理内存的 1~2 倍。
* （ 3 ）高级分区方案：
/boot ：用来存放与 Linux 系统启动有关的程序，比如启动引导装载程序等，建议大小为 100MB 。 
/usr ：用来存放 Linux 系统中的应用程序，其相关数据较多，建议大于 3GB 以上。 
/var ：用来存放 Linux 系统中经常变化的数据以及日志文件，建议大于 1GB 以上。 
/home ：存放普通用户的数据，是普通用户的宿主目录，建议大小为剩下的空间。 
/ ： Linux 系统的根目录，所有的目录都挂在这个目录下面，建议大小为 5GB 以上。 
/tmp ：将临时盘在独立的分区，可避免在文件系统被塞满时影响到系统的稳定性。建议大小为 500MB 以上。 
swap ：实现虚拟内存，建议大小是物理内存的 1~2 倍。
