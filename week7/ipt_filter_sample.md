### 这是一个关于iptalbes之filter表的小案例  
> filter主要用于过滤数据包。  
  * INPUT链：访问（进入）本机的数据包  
  * OUTPUT链：（送出）本机回应的数据包  
  * FORWARD：跟本机无关的包  

* 案例一：  
  案例需求：只针对filter表，预设策略INPUT链DROP，然后针对10.148.60.0/24开通22端口，对所有网段开放80端口，对所有网段开放21端口。因为规则较多，可以采用写脚本。  

``` 
#!/bin/bash
ipt="/usr/sbin/iptables" #iptables命令多次用到，所以设置一个变量简化
$ipt -F #清楚所有规则
$ipt -P INPUT DROP #设置默认策略，使进入INPUT链的数据全部DROP掉
$ipt -P OUTPUT ACCEPT # 设置OUTPUT链的数据全部允许
$ipt -P FORWARD ACCEPT # 设置FORWARD链的数据全部允许
$ipt -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT #这条规则的意思是使通信更加流畅
$ipt -A INPUT -s 10.148.60.0/24 -p tcp --dport 22 -j ACCEPT #放行10.148.60.0网段的22端口
$ipt -A INPUT -p tcp --dport 80 -j ACCEPT
$ipt -A INPUT -p tcp --dport 21 -j ACCEPT
```  

* 案例二：  
  禁止本机被ping  
  ```iptables -I INPUT -p icmp --icmp-type 8 -j DROP```  



