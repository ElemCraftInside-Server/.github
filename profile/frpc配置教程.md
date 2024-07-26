# Frpc配置流程

## 1.使用wget命令下载frp软件压缩包

    wget https://github.com/fatedier/frp/releases/download/v0.59.0/frp_0.59.0_linux_amd64.tar.gz

*如果远程主机连接Github太慢，建议手动下载压缩包后上传到服务器*

## 2.将压缩包移动到/opt目录下并解压
- 移动压缩包

	`mv frp_0.59.0_linux_amd64.tar.gz /opt`
- 解压

	`tar -zxvf frp_0.59.0_linux_amd64.tar.gz`

![](https://pic.imgdb.cn/item/66a3dde8d9c307b7e9eabc57.png)

## 3.编辑frpc.toml文件
建议参考这份文档 [详解 frpc.toml 配置文件](https://freefrp.net/docs)
> 以MCSM面板的配置为例

```
serverAddr = "110.42.33.58"
serverPort = 7700
auth.method = "token"
auth.token = "**********************"
# token非常重要，不要外传，我会发送到企业邮箱里

[[proxies]]
name = "MCSMweb"
type = "http"
localIP = "127.0.0.1"
localPort = 23333
customDomains = ["r730test.ecismc.com"]
```

## 4. 保存后运行
使用`sudo ./frps -c ./frps.toml`命令
看到`success`字样则代表启动成功

![](https://pic.imgdb.cn/item/66a3dde8d9c307b7e9eabc47.png)

## 5. 使用systemd管理Frpc并配置开机自启动
添加systemd配置文件

`sudo vim /etc/systemd/system/frpc.service`

根据官方文档编辑文件内容如下

```
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /opt/frp_0.59.0_linux_amd64/frpc -c /opt/frp_0.59.0_linux_amd64/frpc.toml
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/bin/kill $MAINPID
Restart=always
RestartSec=1min

[Install]
WantedBy = multi-user.target
```

## 6.设置Frpc开机启动
`sudo systemctl enable frpc`

## 7.启动frp（终止frp把start改为stop即可）
`sudo systemctl start frpc`

## 8.查看frpc运行状态
`sudo systemctl status frpc`

![](https://pic.imgdb.cn/item/66a3dde8d9c307b7e9eabc36.png)

