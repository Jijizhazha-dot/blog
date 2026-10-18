---
title: Linux 常用命令速查：按功能分成 10 类
date: 2026-10-19
tag: 工具
minutes: 10
---

命令不必背，但要知道去哪儿找。这份清单按使用场景把常用命令分成 10 类，每条只保留一句作用和最短能跑起来的示例，当速查表用：遇到不熟的，先按类别找到它，再看示例改参数。

## 目录

1. 文件和目录
2. 进程查看与管理
3. 磁盘与文件系统
4. 用户与权限
5. 网络
6. 文本处理
7. 系统状态
8. 软件包管理
9. 计划任务与后台作业
10. 其他常用

## 1. 文件和目录

| 命令 | 作用 | 示例 |
|---|---|---|
| `pwd` | 显示当前所在目录 | `pwd` |
| `ls` | 列出目录内容 | `ls -l` 看详细信息 |
| `cd` | 切换目录 | `cd /home` |
| `mkdir` | 新建目录 | `mkdir myDir` |
| `rmdir` | 删除空目录 | `rmdir myDir` |
| `rm` | 删除文件或目录 | `rm -rf myDir` 递归删除 |
| `touch` | 新建空文件 | `touch file.txt` |
| `cp` | 复制文件或目录 | `cp file.txt /tmp` |
| `mv` | 移动或重命名 | `mv file.txt newfile.txt` |
| `find` | 按条件搜索文件 | `find /home -name "*.log"` |
| `locate` | 从数据库里快速查找 | `locate file.txt` |
| `stat` | 查看文件详细属性 | `stat file.txt` |
| `tree` | 树形展示目录结构 | `tree /home` |

## 2. 进程查看与管理

| 命令 | 作用 | 示例 |
|---|---|---|
| `ps` | 查看进程状态 | `ps aux` |
| `top` | 动态查看资源占用 | `top` |
| `htop` | 更友好的 top | `htop` |
| `kill` | 结束指定进程 | `kill -9 12345` |
| `killall` | 按名称结束全部同名进程 | `killall nginx` |
| `pkill` | 按名称匹配结束进程 | `pkill -9 java` |
| `jobs` | 查看后台任务 | `jobs` |
| `bg` | 把任务切到后台 | `bg %1` |
| `fg` | 把后台任务调回前台 | `fg %1` |

## 3. 磁盘与文件系统

| 命令 | 作用 | 示例 |
|---|---|---|
| `df` | 查看磁盘使用率 | `df -h` |
| `du` | 统计目录或文件大小 | `du -sh /var/log` |
| `lsblk` | 列出块设备 | `lsblk` |
| `mount` | 挂载文件系统 | `mount /dev/sdb1 /mnt` |
| `umount` | 卸载文件系统 | `umount /mnt` |
| `fdisk` | 管理磁盘分区 | `fdisk /dev/sdb` |
| `mkfs` | 格式化分区 | `mkfs.ext4 /dev/sdb1` |
| `blkid` | 查看分区 UUID 与文件系统类型 | `blkid` |
| `fsck` | 检查并修复文件系统 | `fsck /dev/sdb1` |

## 4. 用户与权限

| 命令 | 作用 | 示例 |
|---|---|---|
| `whoami` | 当前登录用户 | `whoami` |
| `id` | 查看 UID、GID 与所属组 | `id` |
| `adduser` | 新建用户 | `adduser username` |
| `usermod` | 修改用户属性 | `usermod -aG sudo username` |
| `passwd` | 改密码 | `passwd username` |
| `chmod` | 改权限 | `chmod 755 file.txt` |
| `chown` | 改属主与属组 | `chown user:group file.txt` |
| `groupadd` | 新建用户组 | `groupadd developers` |
| `groups` | 查看用户所属组 | `groups username` |

## 5. 网络

| 命令 | 作用 | 示例 |
|---|---|---|
| `ping` | 测试连通性 | `ping example.com` |
| `ifconfig` | 查看或配置网卡 | `ifconfig` |
| `ip` | 查看或管理网络接口 | `ip addr show` |
| `netstat` | 查看连接与监听端口 | `netstat -tuln` |
| `ss` | 查看连接（netstat 的现代替代） | `ss -tuln` |
| `curl` | 发 HTTP 请求 | `curl https://example.com` |
| `wget` | 下载文件 | `wget https://example.com/file.zip` |
| `scp` | 远程复制文件 | `scp file.txt user@host:/path` |
| `ssh` | 远程登录 | `ssh user@host` |

## 6. 文本处理

| 命令 | 作用 | 示例 |
|---|---|---|
| `cat` | 输出文件内容 | `cat file.txt` |
| `tac` | 倒序输出 | `tac file.txt` |
| `more` | 分页查看 | `more file.txt` |
| `less` | 分页查看，可上下滚动 | `less file.txt` |
| `head` | 看前 N 行 | `head -n 5 file.txt` |
| `tail` | 看后 N 行 | `tail -n 5 file.txt` |
| `grep` | 按内容搜索 | `grep "error" log.txt` |
| `cut` | 按列截取 | `cut -d ',' -f 1 file.csv` |
| `awk` | 按列处理与统计 | `awk -F ',' '{print $1}' file.csv` |
| `sed` | 流式替换 | `sed 's/old/new/g' file.txt` |

## 7. 系统状态

| 命令 | 作用 | 示例 |
|---|---|---|
| `uname` | 查看内核与系统信息 | `uname -a` |
| `uptime` | 运行时长与负载 | `uptime` |
| `dmesg` | 内核启动日志 | `dmesg` |
| `free` | 内存使用 | `free -h` |
| `vmstat` | 系统性能概览 | `vmstat` |
| `iostat` | CPU 与磁盘 I/O | `iostat` |
| `sar` | 历史性能数据 | `sar 1 5` |

## 8. 软件包管理

| 命令 | 作用 | 示例 |
|---|---|---|
| `apt` | Debian / Ubuntu 系包管理 | `apt update && apt upgrade` |
| `yum` | CentOS / RHEL 系包管理 | `yum install httpd` |
| `dnf` | 新一代 yum | `dnf install nginx` |
| `rpm` | RPM 包工具 | `rpm -ivh package.rpm` |

## 9. 计划任务与后台作业

| 命令 | 作用 | 示例 |
|---|---|---|
| `crontab` | 管理周期任务 | `crontab -e` |
| `at` | 一次性定时任务 | `at now + 5 minutes` |
| `nohup` | 断开终端后继续运行 | `nohup ./script.sh &` |

## 10. 其他常用

| 命令 | 作用 | 示例 |
|---|---|---|
| `alias` | 给命令起别名 | `alias ll='ls -l'` |
| `history` | 命令历史 | `history` |
| `clear` | 清屏 | `clear` |
| `echo` | 输出文本或变量 | `echo "Hello, Linux!"` |
| `date` | 当前日期时间 | `date` |
| `cal` | 日历 | `cal` |
| `shutdown` | 关机 | `shutdown -h now` |
| `reboot` | 重启 | `reboot` |

## 总结

单条命令都不难，难的是把它们串起来：`ps aux` 找出进程，`grep` 过滤，`kill` 收尾；`df -h` 发现磁盘满，`du -sh` 逐层定位，`find` 清理。用熟了，`|` 和重定向比记命令本身更有价值。

想熟练没有别的办法，就是多在自己机器上敲——装个虚拟机或者 WSL，坏了重来成本很低。

---

本文参考站外的 Linux 命令整理而成，示例经过改写与核对。

来源：[Linux常用命令 · 知识库](https://markstao.github.io/my-blog/article/Linux/Linux%E5%91%BD%E4%BB%A4.html)
