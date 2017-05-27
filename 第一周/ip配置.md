* **最小化安装完毕后没有ifconfig工具**  
```yum -y install net-tools```

* **配置ip**
  1. 动态  
    直接用命令 ```dhclient```  
    dhclient命令使用方法：  
        ```dhclient ``` （获取ip）  
        ```dhclient -r``` （释放ip）
    
  2. 静态  
    配置文件位于/etc/sysconfig/network-scripts下，一般以ifcfg-ens33之类的名字呈现  
    用vim编辑配置文件 ```vim /etc/sysconfig/network-scripts/ifcfg-ens33```  
    其配置文件主要释义：  
        TYPE=Ethernet        #网络接口类型  
        BOOTPROTO=static     #动态为dhcp  
        NAME=ens33    #网卡名称  
        ONBOOT=yes          #开启自动启用网络连接  
        IPADDR=192.168.1.20  #设置IP地址  
        GATEWAY=192.168.1.1  #设置网关  
        PREFIX=24           #设置子网掩码  
        DNS1=192.168.1.1     #设置DNS（常用119.29.29.29）  
        HWADDR=00:0C:29:B8:2B:07 #网卡mac地址  

##### 配置完了之后不要忘记重启网络服务  
    ```systemctl restart network.service``` 



* **小技巧**  
    ```vim /etc/sysconfig/network-scripts/ifcfg-ens33``` 网卡配置文件的路径比较长，可以使用一个alias命令，来简化输入过程，配置如下：  
    ``` alias vieth='vim /etc/sysconfig/network-scripts/ifcfg-ens33' ```  
    如此配置后，下次你要配置网卡文件，你只要在命令行输入 vieth就可以直接配置ens33网卡的配置文件了。但这种方法重启后会失效。  
 *一劳永逸的方法*：  
    ``` vi /root/.bashrc ``` 编辑这个文件，如图：  
![image](https://github.com/billxq/notes/blob/master/images/bashrc.jpg)  

### VMWare 虚拟机与主机的三种通信方式  
1. Bridged方式  
虚拟系统的IP可以设置成与本机系统在同一个网段，虚拟机相当于网络内部一个独立的机器，与本机共同插在一个Hub上，网络内的其他机器可以访问虚拟机，虚拟机也可以访问网络内其他机器，当然与本机的互访也不成问题。主机拔掉网线后，虚拟机无法与主机通过网络的方式进行通讯。  

2. NAT方式（需要用vmnet8）  
使用VMware提供的NAT和DHCP服务，虚拟机使用主机中过的虚拟网卡Vmnet8作为网关，这种方式可以实现主机和虚拟机通信，虚拟机也能够访问互联网，但是互联网不能访问虚拟机。只需要设置虚拟机的网络为DHCP，就可以ping通Vmnet8了。也可以手动设置IP，ip设置与vmnet8同网段,gateway，netmask，broadcast设置与vmnet8相同,dns设置与主机相同。如果使用NAT方式：确保Eidt-Virtual Network Editor中的DHCP处于Start状态

3. host-only方式（需要用vmnet1）  
只能进行虚拟机和主机之间的网络通信，虚拟机不能访问外部网络。将虚拟机ip设置与vmnet1同网段,gateway设置成vmnet1的ip,其余设置与vmnet1相同,dns设置与主机相同。

** 对于所有的联网方式：注意关闭防火墙 **

  
