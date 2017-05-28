输入如下命令，进入对应目录，编辑文件：  
1.vim /etc/sysconfig/grub
然后，往这个文件中添加“net.ifnames=0 biosdevname=0”内容，如下图所示：  
![image](https://github.com/billxq/notes/blob/master/images/0.png)

上图中，红框部分是我所添加的内容（注意它的位置）。
紧接着，执行如下命令：

1.grub2-mkconfig -o /boot/grub2/grub.cfg  
效果如下：  
![image](https://github.com/billxq/notes/blob/master/images/1.png)


然后，重启系统后查看网卡名称：  
![image](https://github.com/billxq/notes/blob/master/images/2.png)

### 注意：  
重启后，需要进入/etc/sysconfig/network-scripts目录，修改ifcfg-ens33为ifcfg-eth0，然后编辑此文件，把DEVICE和NAME的值设为eth0.
