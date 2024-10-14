---
title: 每天一个 Linux 命令（52） -- ifconfig
date: 2018/02/21
tags: Linux
description: Some old articles may have been deprecated.
---

配置和显示Linux系统网卡的网络参数

## 补充说明

**ifconfig命令** 被用于配置和显示Linux内核中网络接口的网络参数。用ifconfig命令配置的网卡信息，在网卡重启后机器重启后，配置就不存在。要想将上述的配置信息永远的存的电脑里，那就要修改网卡的配置文件了。

## 语法

``` plain
ifconfig (参数)
```
## 参数

``` plain
add&lt;地址&gt;：设置网络设备IPv6的ip地址；
del&lt;地址&gt;：删除网络设备IPv6的IP地址；
down：关闭指定的网络设备；
<hw<网络设备类型>&lt;硬件地址&gt;：设置网络设备的类型与硬件地址；</hw<网络设备类型>
io_addr<i o地址="">：设置网络设备的I/O地址；</i><i o地址="">
irq<irq地址>：设置网络设备的IRQ；</irq地址>
media&lt;网络媒介类型&gt;：设置网络设备的媒介类型；
mem_start&lt;内存地址&gt;：设置网络设备在主内存所占用的起始地址；
metric&lt;数目&gt;：指定在计算数据包的转送次数时，所要加上的数目；
mtu&lt;字节&gt;：设置网络设备的MTU；
netmask&lt;子网掩码&gt;：设置网络设备的子网掩码；
tunnel&lt;地址&gt;：建立IPv4与IPv6之间的隧道通信地址；
up：启动指定的网络设备；
-broadcast&lt;地址&gt;：将要送往指定地址的数据包当成广播数据包来处理；
-pointopoint&lt;地址&gt;：与指定地址的网络设备建立直接连线，此模式具有保密功能；
-promisc：关闭或启动指定网络设备的promiscuous模式；
IP地址：指定网络设备的IP地址；
网络设备：指定网络设备的名称。
</i>```
### 实例

 **显示网络设备信息（激活状态的）：**

``` plain
[root@localhost ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:16:3E:00:1E:51
          inet addr:10.160.7.81  Bcast:10.160.15.255  Mask:255.255.240.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:61430830 errors:0 dropped:0 overruns:0 frame:0
          TX packets:88534 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:3607197869 (3.3 GiB)  TX bytes:6115042 (5.8 MiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:56103 errors:0 dropped:0 overruns:0 frame:0
          TX packets:56103 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:5079451 (4.8 MiB)  TX bytes:5079451 (4.8 MiB)
```
说明：

 **eth0** 表示第一块网卡，其中`HWaddr`表示网卡的物理地址，可以看到目前这个网卡的物理地址(MAC地址）是`00:16:3E:00:1E:51`。

 **inet addr** 用来表示网卡的IP地址，此网卡的IP地址是`10.160.7.81`，广播地址`Bcast:10.160.15.255`，掩码地址`Mask:255.255.240.0`。

 **lo** 是表示主机的回坏地址，这个一般是用来测试一个网络程序，但又不想让局域网或外网的用户能够查看，只能在此台主机上运行和查看所用的网络接口。比如把 httpd服务器的指定到回坏地址，在浏览器输入127.0.0.1就能看到你所架WEB网站了。但只是您能看得到，局域网的其它主机或用户无从知道。

<li>第一行：连接类型：Ethernet（以太网）HWaddr（硬件mac地址）。
</li>
<li>第二行：网卡的IP地址、子网、掩码。
</li>
<li>第三行：UP（代表网卡开启状态）RUNNING（代表网卡的网线被接上）MULTICAST（支持组播）MTU:1500（最大传输单元）：1500字节。
</li>
<li>第四、五行：接收、发送数据包情况统计。
</li>
<li>第七行：接收、发送数据字节数统计信息。
**启动关闭指定网卡：**
</li>

``` plain
ifconfig eth0 up
ifconfig eth0 down
```
`ifconfig eth0 up`为启动网卡eth0，`ifconfig eth0 down`为关闭网卡eth0。ssh登陆linux服务器操作要小心，关闭了就不能开启了，除非你有多网卡。

 **为网卡配置和删除IPv6地址：**

``` plain
ifconfig eth0 add 33ffe:3240:800:1005::2/64    #为网卡eth0配置IPv6地址
ifconfig eth0 del 33ffe:3240:800:1005::2/64    #为网卡eth0删除IPv6地址
```
 **用ifconfig修改MAC地址：**

``` plain
ifconfig eth0 hw ether 00:AA:BB:CC:dd:EE
```
 **配置IP地址：**

``` plain
[root@localhost ~]# ifconfig eth0 192.168.2.10
[root@localhost ~]# ifconfig eth0 192.168.2.10 netmask 255.255.255.0
[root@localhost ~]# ifconfig eth0 192.168.2.10 netmask 255.255.255.0 broadcast 192.168.2.255
```
 **启用和关闭arp协议：**

``` plain
ifconfig eth0 arp    #开启网卡eth0 的arp协议
ifconfig eth0 -arp   #关闭网卡eth0 的arp协议
```
 **设置最大传输单元：**

``` plain
ifconfig eth0 mtu 1500    #设置能通过的最大数据包大小为 1500 bytes
```
