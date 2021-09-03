## 1.OSI（开放式系统互联）7层参考模型

应用层

表示层

会话层

传输控制层

网络层

链路层

物理层  

## 2.TCP/IP协议模型

应用层

传输控制层

网络层

链路层

物理层

## 2.应用层

应用程序和消息传输的底层网络提供接口

## 3.传输控制层

面向连接的可靠的传输协议

1.三次握手

client -> server   syn(Synchronize Sequence Numbers 同步序列编号)

server -> client   syn+ack（Acknowlege character 确认字符）

client -> server   ack （确认字符）

2.四次挥手

client  -> server FIN

server -> client FIN + ACK

server -> client FIN

client -> server ACK



tcpdump  抓包

tcpdump  -nn -i eth0 port 80

nc www.baidu.com 80

netstat -natp



