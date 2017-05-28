
### 开机启动流程

grub and boot  
  ● Centos5，6的开机启动流程  
  ● grub  
  ● Centos7的开机启动流程  
Centos5,6的开机启动流程  
![image](https://github.com/billxq/notes/blob/master/images/5281.jpeg)  
![image](https://github.com/billxq/notes/blob/master/images/5282.jpeg)  

### initrd / initramfs  
一般存储在/boot目录下，以.img为结尾的文件，是一个小型的根目录系统的映像文件，里面存放了各类系统必须的模组，为了解决内核在加载完成之后没有模组无法访问磁盘加载rootfs的问题。  
### rc  
在inittab的文件中，有这么一行命令　si::sysinit:/etc/rc.d/rc.sysinit ，这句话表示将设置的run level的值作为参数赋予给rc这个脚本。  
我们先来看一下rc，打开/etc/rc.d/rc：
   最主要的两段：  
        for i in /etc/rc$runlevel.d/K* ; do              #循环列出/etc/rc#.d目录下以K开头的文件并赋值给i。  

            # Check if the subsystem is already up.
            subsys=${i#/etc/rc$runlevel.d/K??}           #做字符串切片，删去第一个/etc/rc$.d/K##的部分。
            [ -f /var/lock/subsys/$subsys -o -f /var/lock/subsys/$subsys.init ] || continue     #如果不存在相同的文件名或者文件名.init的服务脚本，则跳出当前这一轮循环。
            check_runlevel "$i" || continue  #检查符号链接是否有效。

            # Bring the subsystem down.
            [ -n "$UPSTART" ] && initctl emit --quiet stopping JOB=$subsys  
            $i stop                                                         #传递当前服务程序stop参数。
            [ -n "$UPSTART" ] && initctl emit --quiet stopped JOB=$subsys       
        done


        for i in /etc/rc$runlevel.d/S* ; do          #循环列出/etc/rc#.d目录下以S开头的文件并赋值给i。

            # Check if the subsystem is already up.
            subsys=${i#/etc/rc$runlevel.d/S??}      subsys      #做字符串切片，删去第一个/etc/rc$.d/S##的部分。
            [ -f /var/lock/subsys/$subsys ] && continue         如果存在与服务脚本相同的文件名的文件就跳出当前这轮循环。
            [ -f /var/lock/subsys/$subsys.init ] && continue        如果存在服务脚本.init的文件名的文件就条和醋当前这轮循环。
            check_runlevel "$i" || continue     检查符号连接是否有效。

            # If we're in confirmation mode, get user confirmation
            if [ "$do_confirm" = "yes" ]; then      
                confirm $subsys
                rc=$?
                if [ "$rc" = "1" ]; then
                    continue
                elif [ "$rc" = "2" ]; then
                    do_confirm="no"
                fi
            fi

            update_boot_stage "$subsys"
            # Bring the subsystem up.   
            [ -n "$UPSTART" ] && initctl emit --quiet starting JOB=$subsys
            if [ "$subsys" = "halt" -o "$subsys" = "reboot" ]; then     如果执行到halt或者reboot脚本，就
                export LC_ALL=C     去除所有本地化的设置
                exec $i start       传递start参数给rc#
            fi
            $i start        传递start参数给服务脚本。
            [ -n "$UPSTART" ] && initctl emit --quiet started JOB=$subsys
        done
功能就是根据选择的run level去传递对应的/etc/rc.d/rc一个数字，然后rc脚本就回去检查相对应的/etc/rc.d/rc#.d目录下的文件，将K[##关闭优先级]开头的文件传递stop参数，将S[##启动优先级]开头的文件传递start参数，来打开关闭服务。 
chkconfig：更新runlevel的启动服务。  
chkconfig [--add | --del] name ：将一个服务添加/删除进系统服务。  
chkconfig --list name：查询一个服务在每一个run level下是否为开机启动。  
chkconfig [--level levels] [--type type] [--no-redirect] name <on|off|reset|resetpriorities>：设置指定的某一个run level下特定服务的状态。  

### grub  
大家都知道在计算机从bios中读取到开机启动顺序之后按序去读取MBR的过程我们称为boot lorder，计算机在这个阶段会去读取MBR中占了446Byte的boot loader中的程序，而在Centos的发行版中基本上都是GRUB这个程序。  
446Byte有多少大呢？446个英文字符，要在这么小的空间中存放一个功能如此强大的程序，是怎么做到的呢？  
grub分为三部分，为stage1，stage1.5和stage2，stage1存放在boot lorder中，stage1指向磁盘中的存放1.5和2的目录（一般为boot），然后通过stage1.5文件的简单驱动来获得/boot的内容得到stage2。  
![image](https://github.com/billxq/notes/blob/master/images/5284.png)  

### grub的设定文件  
/boot/grub/grub.conf  
有些boot目录中会有menu.lst文件，仔细看哦这个文件是通过软链接的形式连到grub.conf文件去的。  
*主要属性：*  
default=0   默认启动的系统，0代表第一个。  
timeout=5   超时时间。  
splashimage=(hd0,6)/boot/grub/splash.xpm.gz  grub的背景画面，这里为指明了splash.xpm这张图片~  
hiddenmenu  是否隐藏grub的启动菜单。  
title Fedora Core (2.6.11-1.1369_FC4)   这个就是我们在grub界面看到的第一个页面的选项内容啦~  
        root (hd0,6)    指定这个系统的根目录  
        kernel /boot/vmlinuz-2.6.11-1.1369_FC4 ro   root=LABEL=/    指定核心以及rootfs  
        initrd /boot/initrd-2.6.11-1.1369_FC4.img   指定initramfs  
title WinXp     这个就是第二个啦，XP哦~  
        rootnoverify (hd0,0)    类似root，但是不测试安装该分区。  
        chainloader +1      GRUB读入分区的第一个扇区的引导记录。  
        
### grub手动指定引导  
grub命令行下：  
grub> find /PATH：可以查询是否有路径。  
grub> root (hd0,1)：指定根设备。  
grub> kernel /vmlinux-version-relias.arch ro grub> root=ROOTFS_PATH：指定内核。  
grub> initrd /initrd-version-relias.arch.img：指定ramfs文件。  
grub> boot：开始引导。  


### grub安装与修复  
命令行中：  
 grub-install --root-directory=/boot /dev/sda ：指定boot目录与stage要写入的磁盘。  
grub命令行中：  
grub> chroot /mnt/sysimage ：挂载真正的rootfs。  
grub> root (hd0,1)：指定boot所在的根目录。  
grub> setup (hd0)：将stage1写入到MBR中。  

### Centos7的开机启动流程  
与Centos5，6相比，Centos7将系统启动的第一支程序从init变成systemd，systemd并不是一个纯粹的init系统，而且还能管理系统的各种daemon，并有用的多个新特性与功能。  
  ● 支持平行启动服务，并按照相依性启动程序  
  ● 按需启动  
  ● 支持快照与状态恢复  
  ● c group预设限制硬件资源 …  
  
### 支持平行启动服务，并按照相依性启动程序  
对比init的脚本启动，systemd大胆的将所有需要启动的都分为一个一个的unit，并以对应的后缀来区分，大致分为：  
  ● 系统服务（.service）  
  ● 挂载点（.mount）  
  ● sockets（.sockets）  
  ● 系统设备（.device）  
  ● 交换分区（.swap）  
  ● 文件路径（.path）  
  ● 启动目标（.target）  
  ● systemd计时器（.timer）  
systemd会为每一个需要启动的服务启动一个套接字，来使不同的daemon之间可以通信，并为每一个daemon分配一个控制组,达到一组一组的按照相依性来启动。  

### 按需启动  
init在系统启动时，会启动所有预设为启动的服务进程，并且系统必须等待所有的服务都启动就绪之后才会允许用户登录操作，这样会让系统启动速度非常慢，并且浪费系统资源。  
systemd就采用了按需启动，在开机时，做到不需要的服务就不启动，在用到之后才回去启用，然后使用完毕后一段时间后就又会关闭。  

### 支持快照与状态恢复  
systemd能够将系统当前的状态保存为快照存贮下来，并且在需要时恢复当前系统状态。  

### target
在systemd中，将原来sysV，upstart一直保留的run level改为了target这种形式，但是target并没有run level划分为0-6，而是更加细致的分了非常多的target。

![images](https://github.com/billxq/notes/blob/master/images/QQ20170528231553.jpg)  
  

### systemd启动流程图  
![image](https://github.com/billxq/notes/blob/master/images/5283.jpeg)  

### systemctl命令
systemctl [options]  

    show [NAME.unittype]：显示指定服务状态或当前允许的服务列表。  
    status [NAME.unittype]：查看指定unit状态。  
    list-units [--type TYPE] [--all]：查看指定unit类型的load，活动状态，运行状态，简述。  
    list-unit-files --type TYPE [--all]:查看指定unit类型的启动状态，是否为开机自启等。  
    start [NAME.unittype]：启动指定unit。
    stop [NAME.unittype]：停止指定unit。
    restart [NAME.unittype]:重新启动unit。
    reload [NAME.unittype]：重新加载unit。
    reset [NAME.unittype]：还原设置unit。
    try-restart [NAME.unittype]：如果服务运行中才会重启

    enable [NAME.unittype]：设置为开机启动。
    disable [NAME.unittype]：设置为开机不启动。

    isolate [NAME.target]：切换到指定target。
    get-default [NAME.target]：查询开机默认target。
    set-default [NAME.target]：设置开机默认target。

    snapshot：创建一个快照，创建完成后在下次重启后，系统会自动回复快照状态。

    hibernate：休眠模式，把信息写入到文件中,也就是硬盘中,不会有断电丢失数据的问题,但恢复时最慢,和重新开机一样。
    sleepp：睡眠模式，把信息到存到内存中,但不能断电,断电后数据丢失,恢复最快。
    hybrid-sleep：混合睡眠模式。
    reboot：重启
    halt:关机

### systemd文件
/etc/systemd/system.conf：systemd设定档。  
/lib/systemd/system/NAME.UNIT：unit脚本文件位置。  
/etc/systemd/system/NAME.UNIT.wants：存放了指定target要启动的所有服务。  

我们来看一下/etc/systemd/system/目录下面到底存放了什么文件。  

    [root@xiao ~]# ll /etc/systemd/system/
    total 32
    drwxr-xr-x. 2 root root 4096 Nov 21  2014 basic.target.wants
    lrwxrwxrwx. 1 root root   37 Nov 21  2014 default.target -> /lib/systemd/system/multi-user.target   注意这个
    drwxr-xr-x. 2 root root 4096 Nov 21  2014 default.target.wants
    drwxr-xr-x. 2 root root 4096 Nov 21  2014 getty.target.wants
    drwxr-xr-x. 2 root root 4096 Apr  8 14:29 multi-user.target.wants
    drwxr-xr-x. 2 root root 4096 Nov 21  2014 printer.target.wants
    drwxr-xr-x. 2 root root 4096 Nov 21  2014 sockets.target.wants
    drwxr-xr-x. 2 root root 4096 Mar 22 17:48 sysinit.target.wants
    drwxr-xr-x. 2 root root 4096 Nov 21  2014 system-update.target.wants

从这个目录我们可以看到，其实系统当前所运行的target是通过软链接的形式链接到/lib/systemd/system/目录下的target文件，基于这种方式，
我们就可以通过手动来切换target。  

    [root@xiao system]#rm -f default.target
    [root@xiao system]#ln -s /lib/systemd/system/graphical.target default.target
