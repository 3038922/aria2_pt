# 分享一个自用的 aria2 用于 pt 下载的配置

## 目前大多数站点都禁止使用 aria2.原因就是目前百度搜的配置都是吸血配置.导致 aria2 下载好就自动删除.或者重启后就丢失正在上传的种子.

- 无论使用 `TR` 还是 `QB`还是其他等等都可以设置吸血的效果.
- 不要让一个开源软件来背负人性的原罪.
- aria2 面对其他软件其实没啥特别的优势.只是喜欢个人喜欢自由的选择软件而已.
- 希望 aria2 早日被各大 PT 站认可.

## 关于安装 (ubuntu 20.04)

- 下载安装 `sudo apt install aria2`
- 手动创建配置文件的文件夹 `sudo mkdir /etc/aria2`
- 创建存档文件 `sudo touch /etc/aria2/aria2.session`
- 创建配置文件 `sudo touch /etc/aria2/aria2.conf`
- 创建日志文件 `sudo touch /etc/aria2/aria2.log`
- 修改读写权限 `sudo chmod -R 777 /etc/aria2`
- 然后把下面的配置 修改好复制粘贴进去.我是用 vim 的. `vim /etc/aria2/aria2.conf`
- 最后启动试试 `sudo aria2c --conf-path=/etc/aria2/aria2.conf -D`

### 配置 conf 需要自行修改的几个地方

- 下载地址也注意修改 `dir=/www/media/`
- RPC 连接密码要注意修改 `rpc-secret=xxxxxx`
- 这里就是每次重启 aria2 上传数据丢失的核心原因.最好手动 `touch aria2.session` 他不会自己生成
- 然后改变权限 `chmod 777 aria2.session`
- 从会话文件中读取下载任务 `input-file=/etc/aria2/aria2.session`
- 在 Aria2 退出时保存错误的、未完成的下载任务到会话文件 `save-session=/etc/aria2/aria2.session`

```
# 文件保存目录
dir=/www/media/
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
disk-cache=32M
# 断点续传
continue=true
# 始终尝试断点续传，无法断点续传则终止下载，默认：true
always-resume=false
# 获取服务器文件时间
remote-time=true

# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
file-allocation=none

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
max-concurrent-downloads=100
# 同一服务器连接数, 添加时可指定, 默认:1
# 官方的aria2最高设置为16, 如果需要设置任意数值请重新编译aria2
max-connection-per-server=16
# 整体下载速度限制, 运行时可修改, 默认:0（不限制）
max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0（不限制）
max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0（不限制）
max-overall-upload-limit=0
# 单个任务上传速度限制, 默认:0（不限制）
max-upload-limit=0
# 禁用IPv6, 默认:false
disable-ipv6=true
# 支持GZip，默认:false
http-accept-gzip=true

# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=4M
# 单个任务最大线程数, 添加时可指定, 默认:5
# 建议同max-connection-per-server设置为相同值
split=64

## 进度保存相关 ##
# 从会话文件中读取下载任务
input-file=/etc/aria2/aria2.session
# 在Aria2退出时保存错误的、未完成的下载任务到会话文件
save-session=/etc/aria2/aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
save-session-interval=1
# 自动保存任务进度时间（秒），0为退出时才保存，默认：60
auto-save-interval=1
# 强制保存会话, 即使任务已经完成, 默认:false
# 开启后会在任务完成后依然保留 .aria2 文件，文件被移除且任务存在的情况下重启后会重新下载。
# 关闭后已完成的任务列表会在重启后清空。
# PT 下载持续做种需求建议开启，防止意外重启后列表被清空。
force-save=true

# 保存通过 WebUI 上传的种子文件(.torrent)，默认:true
# 所有涉及种子文件保存的选项都建议开启，不保存种子文件重启会导致任务丢失。
rpc-save-upload-metadata=true

# 当下载的是一个种子文件(.torrent)时, 自动开始下载, 默认:true，可选：false|mem
# true：保存种子文件
# false：仅下载种子文件
# mem：将种子保存在内存中
follow-torrent=true
# 保存磁力链接元数据为种子文件(.torrent), 默认:false
bt-save-metadata=true
# 加载已保存的元数据文件(.torrent)，默认:false
bt-load-saved-metadata=true
# 删除 BT 下载任务中未选择文件，默认:false
bt-remove-unselected-file=true

## RPC 设置 ##
# 启用 JSON-RPC/XML-RPC 服务器, 默认:false
enable-rpc=true
# 接受所有远程请求, 默认:false
rpc-allow-origin-all=true
# 允许外部访问, 默认:false
rpc-listen-all=true
# RPC 监听端口, 默认:6800
rpc-listen-port=6800
# RPC 密钥 这里自己改
rpc-secret=xxxxxx
# RPC 最大请求大小
rpc-max-request-size=10M
# RPC 服务 SSL/TLS 加密, 默认：false
# 启用加密后必须使用 https 或者 wss 协议连接
# 不推荐开启，建议使用 web server 反向代理，比如 Nginx、Caddy ，灵活性更强。
#rpc-secure=false
# 在 RPC 服务中启用 SSL/TLS 加密时的证书文件(.pem/.crt)
#rpc-certificate=/root/.aria2/xxx.pem
# 在 RPC 服务中启用 SSL/TLS 加密时的私钥文件(.key)
#rpc-private-key=/root/.aria2/xxx.key
# 事件轮询方式, 可选：epoll, kqueue, port, poll, select, 不同系统默认值不同
#event-poll=select



## BT/PT下载相关 ##
# BT监听端口(TCP)
bt-external-ip=61.130.53.150
listen-port=51414

# 客户端伪装, PT需要
peer-id-prefix=-TR2940-
user-agent=Transmission/2.94
peer-agent=Transmission/2.94


# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true

# 单个种子最大连接数, 默认:55 0表示不限制
bt-max-peers=0
# 最小分享率
seed-ratio = 0
# 最小做种时间, 单位:分
# seed-time = 60000
# 分离做种任务
bt-detach-seed-only=true
#日志设置
log-level=warn
log=/etc/aria2/aria2.log
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=false
# # 打开IPv6 DHT功能, PT需要禁用
enable-dht6=false
# 本地节点查找, PT需要禁用, 默认:false
bt-enable-lpd=false
# # 种子交换, PT需要禁用, 默认:true
enable-peer-exchange=false
# # 每个种子限速, 对少种的PT很有用, 默认:50K
bt-request-peer-speed-limit=10M
```

## 关于把 aria2 加入 service (ubuntu 20.04)

- 请使用 vim 等操作.我当时直接宝塔操作提示各种奇葩错误.
- 主要为了实现
- 启动 `service aria2c start`
- 停止 `service aria2c stop`
- 启动 `service aria2c status`
- 重启 `service aria2c restart`
- 这行密码改下 `sudo -u root -p 账户密码 aria2c --conf-path=/etc/aria2/aria2.conf -D`

### 具体流程

- 创建配置文件 `sudo vim /init.d/aria2c`
- 复制粘贴下面的东西,并修改密码 vim 操作比较反人类 也可以使用`gedit`等
- 先按`:` 再输入 `wq`保存退出
- 然后重新加载 aria2 init.d 的配置 `systemctl daemon-reload`
- 最后`service aria2c start试试`

```
#!/bin/sh
### BEGIN INIT INFO
# Provides:          aria2
# Required-Start:    $remote_fs $network
# Required-Stop:    $remote_fs $network
# Default-Start:    2 3 4 5\
# Default-Stop:      0 1 6\
# Short-Description: Aria2 Downloader
### END INIT INFO

case "$1" in

start)

echo -n "Starting aria2c"

sudo aria2c --conf-path=/etc/aria2/aria2.conf -D

;;

stop)

echo -n "Shutting down aria2c "

killall aria2c

;;

restart)

killall aria2c

sudo -u root -p 账户密码 aria2c --conf-path=/etc/aria2/aria2.conf -D

;;

esac

exit
```
