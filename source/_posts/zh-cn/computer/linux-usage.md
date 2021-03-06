---
title: linux-usage
category: computer
date: 2020-02-08 16:42:06
tags:
toc: true
---

一份我的 Linux 手扎

<!-- more -->

## linux命令

### sed

从Next的Katex行内式 `$...$` 迁移到Icarus的Katex行内式 `\\(...\\)`
```console
$ sed -i 's,\$\([^$]*\)\$,\\\\(\1\\\\),g' file.md
```

### grep

根据文件内容搜索文件

```console
$ grep -iRl "your-text-to-find" ./
```

### rsync

1. 拷贝文件, 保留所有信息
   ```console
   $ rsync -aXv -P $SOURCE_DIR/ $TARGET_DIR/`
   ```
2. 仅复制, 不保留权限
   ```console
   $ rsync -rlt -P --no-owner --no-group --no-perms $SOURCE_DIR/ $TARGET_DIR/
   ```
3. 同步文件夹(小心, 有--delete参数, 会删光目标文件夹多余的文件)
   ```console
   $ rsync -aXHAz -v -P --exclude={"filename1","path/to/filename2"} --delete $SOURCE_DIR/ $TARGET_DIR/
   ```

### ss
ss is a member of iproute tools set
ss has only 22 parameters

列出套接字统计
```console
$ ss -s
```

查看端口占用
```console
# ss -nlptu | grep <端口号>
```

### dd

备份GPT分区表
```console
# dd if=/dev/sda of=gpt-partition.bin bs=512 count=34
```

恢复GPT分区表
```console
# dd if=gpt-partition.bin of=/dev/sda bs=512 count=34
```

### 生成随机密码

生成20个长度为12的 含大写字母 `-c` , 数字 `-n` , 符号 `-y` , 完全随机 `-s` 的密码

```console
$ pwgen -cnys 12 20
```

### Recursively chmod all directories except files

To recursively give directories read&execute privileges:

```console
# find /path/to/base/dir -type d -exec chmod 755 {} +
```

To recursively give files read privileges:

```console
# find /path/to/base/dir -type f -exec chmod 644 {} +
```

Reference from [StackExchange](https://superuser.com/questions/91935/how-to-recursively-chmod-all-directories-except-files)

### 带排除项删除文件和目录

```console
# shopt -s extglob    #打开extglob模式
# rm -fr !(filename)
```

### SWAP file

```console
# truncate -s 0 /swapfile
# chattr +C /swapfile
# btrfs property set /swapfile compression none
```

### ssh-keygen

1. 查看某个 key 的公钥指纹
   ```console
   $ ssh-keygen -l -f </path/to/ssh/key>
   $ ssh-keygen -l -E md5 -f <Your key> # MD5 格式
   ```
2. 修改某个 key 的 Comment
   ```console
   $ ssh-keygen -c -C <Your comment> -f <Your key>
   ```
3. 输出某个 key 的公钥格式
   ```console
   $ ssh-keygen -y -f <Your key>
   ```

### 泛域名证书

```console
$ certbot certonly --preferred-challenges dns --manual  -d *.example.com
```

### Kernel interface

1. 获取uuid
   ```console
   $ cat /proc/sys/kernel/random/uuid
   ```
2. 查看熵池
   ```console
   cat /proc/sys/kernel/random/entropy_avail
   ```
3. 查看电池电量
   ```console
   $ cat /sys/class/power_supply/<Your battery name>/capacity
   ```

### 透明代理(TPROXY)客户端命令

```console
# 设置策略路由
ip rule add fwmark 1 table 100
ip route add local 0.0.0.0/0 dev lo table 100

# 代理局域网设备
iptables -t mangle -N V2RAY
iptables -t mangle -A V2RAY -d 127.0.0.1/32 -j RETURN
iptables -t mangle -A V2RAY -d 224.0.0.0/4 -j RETURN
iptables -t mangle -A V2RAY -d 255.255.255.255/32 -j RETURN
iptables -t mangle -A V2RAY -d 192.168.0.0/16 -p tcp -j RETURN # 直连局域网，避免 V2Ray 无法启动时无法连网关的 SSH，如果你配置的是其他网段（如 10.x.x.x 等），则修改成自己的
iptables -t mangle -A V2RAY -d 192.168.0.0/16 -p udp -j RETURN # 直连局域网
iptables -t mangle -A V2RAY -p udp -j TPROXY --on-port 12345 --tproxy-mark 1 # 给 UDP 打标记 1，转发至 12345 端口
iptables -t mangle -A V2RAY -p tcp -j TPROXY --on-port 12345 --tproxy-mark 1 # 给 TCP 打标记 1，转发至 12345 端口
iptables -t mangle -A PREROUTING -j V2RAY # 应用规则

# 代理网关本机
iptables -t mangle -N V2RAY_MASK
iptables -t mangle -A V2RAY_MASK -d 224.0.0.0/4 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 255.255.255.255/32 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 192.168.0.0/16 -p tcp -j RETURN # 直连局域网
iptables -t mangle -A V2RAY_MASK -d 192.168.0.0/16 -p udp ! --dport 53 -j RETURN # 直连局域网，53 端口除外（因为要使用 V2Ray 的 DNS）
iptables -t mangle -A V2RAY_MASK -j RETURN -m mark --mark 0xff    # 直连 SO_MARK 为 0xff 的流量(0xff 是 16 进制数，数值上等同与上面V2Ray 配置的 255)，此规则目的是避免代理本机(网关)流量出现回环问题
iptables -t mangle -A V2RAY_MASK -p udp -j MARK --set-mark 1   # 给 UDP 打标记,重路由
iptables -t mangle -A V2RAY_MASK -p tcp -j MARK --set-mark 1   # 给 TCP 打标记，重路由
iptables -t mangle -A OUTPUT -j V2RAY_MASK # 应用规则
```

[参考来源](https://guide.v2fly.org/app/tproxy.html)

## bash

### if 判断

```
# 用于数字判断
-eq 相等
-ne 不等
-gt 大于
-ge 大于等于
-lt 小于
-le 小于等于

# 用于文件判断
-r 可读
-w 可写
-x 可执行
-f 是否是标准文件
-d 是否是目录
-c 是否是字符特殊文件
-b 是否是块特殊文件
-s 文件大小非0时为真
-t 当文件描述符(默认为1)指定的设备为终端时为真

# 用于字符串判断
string1 = string2 and string1 == string2 - The equality operator returns true if the operands are equal.
    Use the = operator with the test [ command.
    Use the == operator with the [[ command for pattern matching.
string1 != string2 - The inequality operator returns true if the operands are not equal.
string1 =~ regex- The regex operator returns true if the left operand matches the extended regular expression on the right.
string1 > string2 - The greater than operator returns true if the left operand is greater than the right sorted by lexicographical (alphabetical) order.
string1 < string2 - The less than operator returns true if the right operand is greater than the right sorted by lexicographical (alphabetical) order.
-z string - True if the string length is zero.
-n string - True if the string length is non-zero.

# 逻辑判断
-a 与
-o 或
! 非
```

## 字符串变量匹配

```
::x 正数时从左往右截取x个, 负数时从右往左截掉x个
:x 从x开始截取后面所有内容, 负数时
:(-x) 或者 : -x 从右往左截取所有内容
:x:y 从x开始截取y个字符
${food:-Cake} 如果 $food 存在否则输出 "Cake"

STR="/path/to/foo.cpp"
echo ${STR%/*}   # /path/to     % 是从后向前匹配, 单个 % 是非贪婪模式, 
echo ${STR#*/}   # path/to/foo.cpp   # 是从前向后匹配, 单个 # 是非贪婪模式, 
echo ${STR%.cpp} # /path/to/foo 两个 %% 是贪婪模式
echo ${STR##*.}  # cpp          两个 ## 是贪婪模式
```