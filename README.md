
# rakshasa



rakshasa是一个使用Go语言编写的强大多级代理工具，专为实现**多级代理**，**内网穿透**而设计。它可以在节点群里面任意两个节点之间转发TCP请求和响应，同时支持**socks5代理**，**http代理**，并且可以**引入外部http、socks5代理池，自动切换请求IP**。

节点之间使用内置证书的TLS加密TCP通讯，再叠加一层自定义秘钥的AES加密，可以在所有Go支持的平台使用。可以在你所有的的Windows和Linux服务器上搭建节点并组成节点群网络。

## 项目结构示例和截图
[点击查看更多介绍](./readme/rakshasa项目设计.md)



## 编译与使用

首先生成证书：

```shell
cd gencert
go run main.go
cd ../
```
也可以使用其他工具生成证书，将 server.crt 和 server.key 放到 cert 目录下。然后再编译rakshasa

```shell
go build
```

在 Windows 下使用cmd跨平台编译 Linux 示例：

```shell
cd gencert
go run main.go
cd ../
set GOOS=linux
go build
```

## 使用图示
![image](https://user-images.githubusercontent.com/128351726/226882870-f4f3cbc0-61df-486c-afc0-511d87586402.png)


## 使用方法



### 启动一个带CLI节点
不带任何参数即可启动：
```shell
d:\>rakshasa.exe
start on port: 8883
rakshasa>
rakshasa>help

Commands:
  bind              进入bind功能
  clear             clear the screen
  config            配置管理
  connect           进入connect功能
  exit              exit the program
  help              display help
  httpproxy         进入httpProxy功能
  new               与一个或者多个节点连接，使用方法 new ip:端口 多个地址以,间隔 如1080 127.0.0.1:1081,127.0.0.1:1082
  ping              ping 节点
  print             列出所有节点
  remoteshell       远程shell
  remotesocks5      进入remotesocks5功能
  shellcode         执行shellcode
  socks5            进入socks5功能


rakshasa>
```
请查阅[CLI使用说明](./readme/cli.md)了解详细信息

## 其他启动参数说明
### -nocli
在无法后台执行的情况下，启动一个不带 CLI 的节点:
```shell
nohup /root/rakshasa -nocli > /root/rakshasa.log 2>&1 &
#Linux下配合nohup后台执行
```

### -p 端口
以指定端口启动:
```shell
rakshasa -p 8883
```

### -d ip:port,ip:port...
连接下一层代理或更多层代理，多个地址以逗号隔开，生效在最后一个 ip:port：
```shell
rakshasa -d 192.168.1.1:8883,192.168.1.2:8883,192.168.1.3:8883 -socks5 1080
#从本地1080端口启动一个socks5代理，流量通过三层转发ip最后在192.168.1.3请求目标数据
```

### -socks5 用户名:密码@ip:端口 
本地开启SOCKS5代理穿透到远程节点，可以不带-d：
```shell
rakshasa -socks5 1080 
#不使用-d参数，则表示直接在本机启动一个socks5代理
```



### -remotesocks5 端口 
远程开启SOCKS5代理流量出口到本地：
```shell
rakshasa -remotesocks5 1081  -d 192.168.1.2:1080,192.168.1.3:1080
#方向从右往左(加上本机是3个节点)，在192.168.1.3这台机器开启一个socks5端口1081，流量穿透到本地节点出去
```

### -connect ip:port,remote_ip:remote_port 
本地监听并转发到指定 IP 端口，使用场景为本机连接 teamserver，隐藏本机 IP：
```shell
rakshasa -connect 127.0.0.1:50050,192,168,1,2:50050 -d 192.168.1.3:1080,192.168.1.4:1080 
#本机cs连接127.0.0.1:50050实际上通过1.3,1.4节点后，再连接到192.168.1.2:50050 teamserver，teamserver看到你的ip是最后一个节点的ip
```

### -bind  ip:port,remote_ip:remote_port
反向代理模式，必须配合-d使用：
```shell
rakshasa -bind 192.168.1.2:50050,0,0,0.0:50050 -d 192.168.1.3:1080,192.168.1.4:1080
#与上面相反，在最右端节点监听端口50050，流量到本机节点后，最终发往192.168.1.2，最终上线ip为本机ip
```
### -http_proxy 用户名:密码@ip:端口
启动一个http代理，可以不使用-d，建议配合-http_proxy_pool使用代理池，自动切换代理ip：
```shell
rakshasa -http_proxy 8080 -http_proxy_pool out.txt
```

### -password 密钥
各节点除了证书校验之外，还额外支持密钥连接，建议使用并定期更换密钥，以避免二进制泄露后被别人连上
```shell
rakshasa -password 123456
```


### -f yaml文件 [详细说明](./readme/config.md)
指定配置文件启动。

### -help 
更多启动参数使用帮助

## 关于开源
本作品使用MPL 2.0许可证，您可以下载、修改和使用本代码。然而，您必须明确表示，任何此类担保、支持、赔偿或责任义务均由您单独提供，与本作者无关。本人不承担您在使用或修改本程序所造成的任何后果或责任。

在遵循MPL 2.0许可证的基础上，您可以自由地对rakshasa进行修改和扩展，以满足您的特定需求。同时，您可以将改进和新功能贡献给社区，让更多人受益。但请注意，确保在分享和发布修改后的代码时遵守许可证要求，并尊重原作者的版权。

## 联系方式
联系我加入微信群聊

QQ: 2252233695

WeChat/微信: Mob20045

## 知识星球
![5d5003546e6618b7c40dc8946963ec7](https://user-images.githubusercontent.com/128351726/226802981-64c09047-a78c-4e7f-b48d-7bfa5439ec5f.jpg)

