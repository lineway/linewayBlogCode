---
title: python网络编程----UDP
date: 2018-12-31 17:20:55
tags: python
categories: 编程学习
---

`IP`协议只负责尝试将每个数据包传输至正确的机器。如果有两个独立的应用程序要维护一个会话的时候，那么还需要`IP`层以上的协议提供以下两个特性：

- 需要为两台主机间传送的数据包打上标签，以便于区分不同进程间的数据包，如网页数据包和电子邮件数据包。这一过程叫做`多路复用(multiplexing`)。
- 两台主机之间独立的数据包发生任何错误，都需要进行修复。如丢失的数据包重新传输、数据包传输的顺序错误时需要重组回正确的顺序、丢弃重复的数据包等。这些特性叫做`可靠传输(relable  transport`)。

使用`IP`层的两个主要协议分别是`用户数据报协议（UDP）`和`传输控制协议（TCP）`。

`UDP`协议解决了上述特性中的第一条，但是使用`UDP`协议的网络仍然需要自己处理丢包、重包和包的乱序问题。`TCP`协议解决了上述特性中提到的两个问题，虽然`UDP`协议和`TCP`协议都使用了端口号的方式解决了多路复用和分解的问题，但是`TCP`协议还保证了数据流的顺序及可靠传输。

当然，还存在一种可能就是有一些专用应用程序在`IP`网络上进行会话时，既不选择`TCP`协议也不选择`UDP`协议，而是创建一个全新的基于`IP`协议来支持新的会话方式。

## 端口号

计算机网络中，需要对共享同一通信信道的多个信号进行区分。`多路复用（multiplexing）`就是允许多个会话共享同一介质或机制的解决方案。

`源端口（source port）`标识了源机器上发送数据包的特定进程或程序；`目标端口（destination port）`则标识了目标IP地址上进行该会话的特定应用程序。如，Web服务器在80端口提供服务，我们使用浏览器访问网站的时候，服务器的响应就是从80端口（源端口）发送数据流到本地的浏览器进程使用的端口。

简而言之，IP网络层保证了两台主机之间的数据包传输；使用IP地址和端口号的方式，使得数据包的传输更具有针对性。UDP机制相当简单，仅使用IP地址和端口号进行标识，以此将数据包发送至目标地址。

端口号的使用并非随机的，一般是按照一下常规方法获得的：

- 惯例：互联网号码分配机构为许多专用服务分配了官方端口号。如：DNS服务的默认端口号是53的UDP端口。
- 自动配置：计算机在接入网络的时候，会使用DHCP协议来获取一些重要服务的IP，应用程序通过将这些IP地址与知名端口结合，便可以访问这些基础服务。
- 手动配置：用户或者网络管理员还可以通过手动配置IP地址或相应的服务域名。

根据相应的服务，端口号主要分为三大类，这一分类规则适用于`UDP`和`TCP`：

- 知名端口(0~1023)：被分配给最重要、最常用的服务。一般而言，普通用户无法监听这一类端口，如上述提及到的53端口；
- 注册端口(1024~49151)：这一类端口无特别之处，一般使用与专有服务，如mysql默认监听的3306。当然用户他也可以编写程序占用这些端口；
- 其余端口(49152~65535)：这部分端口是可以随意使用的，如果用户编写的程序没有指定端口号的时候，操作系统一般会从这一类端口中随机选取端口号。

## 套接字（socket）

套接字是一个通信端点，操作系统使用整数来标识套接字。使用python的`socket`模块提供的`fileno()`方法可以查看对象的整数标识符。调用`socket.socket`对象的方法请求使用套接字系统时，`socket.socket`对象都会自动使用内部维护的整数标识符。

示例：

```python
import argparse
import socket
from datetime import datetime

MAX_BYTES =65535

def server(port):
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind(('127.0.0.1', port))
    print("Listen at {}".format(sock.getsockname()))
    while True:
        data, address = sock.recvfrom(MAX_BYTES)
        # text = data.decode("acsii")
        text = data
        print("The client at {} says {!r}".format(address, text))
        text = "Your data was {} bytes long".format(len(data))
        data = text.encode("ascii")
        sock.sendto(data, address)


def client(port):
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    text = "The time is {}".format(datetime.now())
    data = text.encode("ascii")
    sock.sendto(data, ("127.0.0.1", port))
    print("The OS assigned me the address {}".format(sock.getsockname()))
    data, address = sock.recvfrom(MAX_BYTES)
    text = data.decode("ascii")
    print("The server {} replied {!r}".format(address, text))


if __name__ == "__main__":
    choices = {"client": client, "server": server}
    parser = argparse.ArgumentParser(description="Send and receive UDP locally")
    parser.add_argument("role", choices=choices, help="which role to play")
    parser.add_argument("-p", metavar="PORT", type=int, default=8080, help="UDP port (default 1060)")
    args = parser.parse_args()
    function = choices[args.role]
    function(args.p)
```

上述代码很好理解。首先，服务器使用`socket()`调用创建了一个空套接字。这个套接字使用`AF_INET`和`SOCK_DGRAM`分别标记了协议族和数据报类型。接着，服务器使用`bind()`命令请求绑定了一个UDP网络地址，这个网络地址是一个python的二元组，包含一个IP地址字符串和一个UDP端口。一旦服务端进入循环，就会不断的运行`recvfrom()`来接收客户端发送的数据报，一旦接收一个数据报，`recvfrom()`就会返回两个值，一个是发送该数据报的客户端地址，另一个是以字节表示的数据报内容。