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





* **小技巧**  
    ```vim /etc/sysconfig/network-scripts/ifcfg-ens33``` 网卡配置文件的路径比较长，可以使用一个alias命令，来简化输入过程，配置如下：  
    ``` alias vieth='vim /etc/sysconfig/network-scripts/ifcfg-ens33' ```  
    如此配置后，下次你要配置网卡文件，你只要在命令行输入 vieth就可以直接配置ens33网卡的配置文件了。但这种方法重启后会失效。  
 *一劳永逸的方法*：  
    ``` vi /root/.bashrc ``` 编辑这个文件，如图：  
![image](https://github.com/billxq/notes/blob/master/images/bashrc.jpg)  

  