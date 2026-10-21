---
title: Linux 常用命令速查：按功能分成 10 类
date: 2026-10-19
time: 09:42
tag: 工具
minutes: 15
featured: true
cover: images/2026-10-19-linux-commands-2.jpg
excerpt: 按十类场景整理 Linux 常用命令，说明用途、参数、权限与使用条件，并提供可复制的文件操作和文本处理练习。
---

# Linux 常用命令速查：按功能分成 10 类

![Linux 常用命令速查封面](images/2026-10-19-linux-commands-2.jpg)

命令不必一次背完。先根据任务找到分类，再查看示例和参数，通常比单纯记忆更有效。

本文以常见 GNU/Linux 发行版和 Bash 为例，兼顾命令速查与动手练习。

## 使用前先看

- **运行环境**：在 Linux 终端中执行。macOS、BusyBox 和其他 Shell 的部分参数可能不同。
- **权限要求**：普通文件练习不需要管理员权限；修改用户、安装软件、挂载设备等操作通常需要 `sudo` 或 root 权限。
- **示例参数**：`username`、`user@host`、`12345`、`/dev/sdb1` 等表示需要按实际情况替换的值。
- **复制规则**：表格中的命令按需单独执行。标注为"完整练习"的代码块可按顺序执行。
- **可选工具**：`tree`、`htop`、`locate`、`iostat`、`sar`、`at`、`cal` 等可能需要额外安装。

容器和 WSL 中的设备、内核信息与后台服务支持可能不同。进行分区、格式化、关机等操作前，应确认当前环境和目标。

本文示例未实机测试。封面为相对路径，发布前请确认图片文件存在。

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

这类命令用于查看路径、创建文件、复制移动，以及按名称查找文件。

下表中涉及的 `file.txt` 等文件，需要先创建；表格不是按顺序执行的脚本。

| 命令 | 用途 | 示例 |
|---|---|---|
| `pwd` | 显示当前目录的路径 | `pwd` |
| `ls` | 列出目录内容 | `ls -lah` |
| `cd` | 切换目录 | `cd "$HOME"` |
| `mkdir` | 创建目录；`-p` 可创建多级目录 | `mkdir -p demo/backup` |
| `rmdir` | 删除空目录 | `rmdir empty-dir` |
| `rm` | 删除文件；`-i` 在删除前询问 | `rm -i file.txt` |
| `touch` | 创建空文件，或更新已有文件的时间戳 | `touch file.txt` |
| `cp` | 复制文件；`-i` 在覆盖前询问 | `cp -i file.txt backup/` |
| `mv` | 移动或重命名；`-i` 在覆盖前询问 | `mv -i file.txt renamed.txt` |
| `find` | 在指定目录中按条件查找 | `find . -type f -name "*.log"` |
| `locate` | 使用索引数据库查找路径 | `locate file.txt` |
| `stat` | 查看大小、权限、时间等属性 | `stat file.txt` |
| `tree` | 以树形结构显示目录 | `tree -L 2 .` |

`ls -lah` 中：

- `-l`：显示详细信息。
- `-a`：包含名称以 `.` 开头的隐藏项目。
- `-h`：以便于阅读的单位显示大小。

`find` 示例中的 `"*.log"` 要加引号，让通配符交给 `find` 处理。

`locate` 依赖索引数据库，结果可能不是最新的，也可能不包含刚创建的文件。

### 完整练习：准备文件并复制、重命名

下面会在临时目录中创建独立练习环境。后文标注"使用练习目录"的示例，均依赖这一段，并应在同一个终端会话中运行。

```bash
demo_dir="$(mktemp -d)"
cd "$demo_dir"
mkdir backup empty-dir
printf 'Hello, Linux!\nold value\n' > file.txt
printf 'INFO service started\nERROR connection failed\n' > app.log
printf 'name,score\nAlice,90\nBob,85\n' > data.csv
cp file.txt backup/
mv backup/file.txt backup/copied.txt
ls -lah
stat file.txt
find . -type f -name "*.txt"
printf '练习目录：%s\n' "$demo_dir"
```

预期结果：

- 当前目录中有 `file.txt`、`app.log` 和 `data.csv`。
- `backup/` 下有 `copied.txt`。
- `find` 会列出两个 `.txt` 文件的相对路径。

### 删除文件与目录

使用上面的练习目录，下面的命令会询问是否删除复制件；删除空目录则不需要递归参数。

```bash
rm -i backup/copied.txt
rmdir empty-dir
```

删除非空目录需要递归参数，例如 `rm -r -i backup`。这里的 `-i` 可能逐项询问。

`rm` 通常不经过桌面回收站。`-f` 会取消部分确认并忽略不存在的目标，日常入门操作不必默认添加。

## 2. 进程查看与管理

先查看进程的名称、PID 和运行状态，再决定是否结束它。

| 命令 | 用途 | 示例 |
|---|---|---|
| `ps` | 查看进程快照 | `ps aux` |
| `top` | 动态查看进程和资源使用 | `top` |
| `htop` | 交互式查看进程，通常需安装 | `htop` |
| `pgrep` | 查找匹配进程并显示 PID | `pgrep -a -x nginx` |
| `kill` | 向指定 PID 发送信号 | `kill -TERM 12345` |
| `killall` | 向同名进程发送信号 | `killall -TERM nginx` |
| `pkill` | 按条件匹配进程并发送信号 | `pkill -TERM -x nginx` |
| `jobs` | 查看当前 Shell 管理的作业 | `jobs -l` |
| `bg` | 让暂停的作业在后台继续 | `bg %1` |
| `fg` | 将作业带回前台 | `fg %1` |

`12345` 是 PID 示例；`nginx` 是进程名称示例。执行终止命令前，应替换为实际目标。按名称发送信号可能影响多个进程，也受当前用户权限限制。

在 `top` 或 `htop` 中，通常按 `q` 退出。

### SIGTERM 与 SIGKILL

`kill PID` 默认发送 SIGTERM，请求程序正常退出。程序通常有机会保存数据和释放资源。

只有在确认目标且普通终止无效时，才考虑：

```bash
kill -KILL 12345
```

SIGKILL 也可写成 `-9`，程序无法捕获该信号并执行退出清理。

### 完整练习：启动并结束一个后台进程

```bash
sleep 300 &
demo_pid=$!
ps -p "$demo_pid" -o pid,comm,args
kill -TERM "$demo_pid"
```

`$!` 表示最近启动的后台作业的进程 ID。

`jobs`、`bg`、`fg` 属于当前交互式 Shell 的作业控制。`%1` 是作业编号，不是系统 PID；使用前先运行 `jobs` 查看。

## 3. 磁盘与文件系统

先使用查询命令确认设备、文件系统和挂载点，再进行修改。

### 查看容量与设备

| 命令 | 用途 | 示例 |
|---|---|---|
| `df` | 查看已挂载文件系统的容量 | `df -h` |
| `df -i` | 查看 inode 使用情况 | `df -i` |
| `du` | 统计目录占用空间 | `du -sh "$HOME"` |
| `lsblk` | 查看块设备、文件系统和挂载点 | `lsblk -f` |
| `blkid` | 查看设备 UUID 和文件系统类型 | `sudo blkid` |
| `fdisk -l` | 列出磁盘分区信息 | `sudo fdisk -l` |

GNU/Linux 下，可用以下命令查看家目录中各项的占用，并按大小排序：

```bash
du -h --max-depth=1 "$HOME" | sort -h
```

部分目录可能因权限限制无法读取。`df` 和 `du` 统计对象不同，结果不一定相同。

### 挂载、分区与格式化

下表中的 `/dev/sdb1` 和 `/dev/sdb` 只是示例设备名。必须根据 `lsblk -f` 的实际结果确认目标，不能直接照抄。

| 命令 | 用途 | 示例或说明 |
|---|---|---|
| `mount` | 将文件系统挂载到目录 | `sudo mount /dev/sdb1 /mnt/demo` |
| `umount` | 卸载文件系统 | `sudo umount /mnt/demo` |
| `fdisk` | 交互式编辑分区表 | `sudo fdisk /dev/sdb`，写入分区表会修改磁盘 |
| `mkfs.ext4` | 创建 ext4 文件系统 | `sudo mkfs.ext4 /dev/sdb1`，会破坏目标分区原有数据 |
| `fsck` | 调用相应工具检查文件系统 | 使用方式取决于文件系统类型和当前状态 |

挂载前应创建挂载目录，并确认设备未被其他用途占用。卸载前，先离开挂载目录并关闭正在使用它的程序。

文件系统修复通常要求先卸载目标文件系统。不要对已挂载的读写文件系统直接执行修复；根文件系统通常需要在恢复环境中处理。XFS、Btrfs 等文件系统应使用各自适用的检查工具和流程。

## 4. 用户与权限

查看当前身份通常不需要管理员权限；创建账户、修改他人密码和调整文件属主通常需要。

下表中的 `username`、`user`、`group` 是需要替换的名称。

| 命令 | 用途 | 示例 |
|---|---|---|
| `whoami` | 显示当前有效用户名 | `whoami` |
| `id` | 查看 UID、GID 和所属组 | `id` |
| `adduser` | 创建用户，行为因发行版而异 | `sudo adduser username` |
| `usermod` | 修改用户属性或附加组 | `sudo usermod -aG sudo username` |
| `passwd` | 修改当前用户密码 | `passwd` |
| `passwd username` | 修改指定用户密码 | `sudo passwd username` |
| `chmod` | 修改文件权限 | `chmod 644 file.txt` |
| `chown` | 修改文件属主与属组 | `sudo chown user:group file.txt` |
| `groupadd` | 创建用户组 | `sudo groupadd developers` |
| `groups` | 查看用户所属组 | `groups` |

### 常用权限写法

```bash
# 文件所有者可读写，其他用户只读
chmod 644 file.txt
# 给文件所有者增加脚本执行权限
chmod u+x script.sh
```

权限位中的 `r`、`w`、`x` 分别表示读、写、执行。目录上的执行权限表示能否进入或遍历该目录。

`usermod -aG` 中，`-G` 指定附加组，`-a` 表示追加。遗漏 `-a` 可能替换用户原有的附加组列表。

Debian、Ubuntu 常见管理组是 `sudo`，部分 RHEL 系发行版使用 `wheel`；实际权限由 sudo 配置决定。组变更后通常需要重新登录。

## 5. 网络

查看地址和端口时，优先使用 `ip`、`ss`。`ifconfig`、`netstat` 属于旧工具，可能需要安装 `net-tools`。

| 命令 | 用途 | 示例 |
|---|---|---|
| `ping` | 发送有限次数的 ICMP 探测 | `ping -c 4 example.com` |
| `ip` | 查看网络接口与地址 | `ip addr show` |
| `ip route` | 查看路由 | `ip route show` |
| `ss` | 查看 TCP、UDP 监听套接字 | `ss -tuln` |
| `ifconfig` | 旧式网络接口工具 | `ifconfig` |
| `netstat` | 旧式连接和端口查看工具 | `netstat -tuln` |
| `curl` | 发起请求，默认将响应正文输出到终端 | `curl https://example.com` |
| `wget` | 下载资源到文件 | `wget -O example.html https://example.com/` |
| `scp` | 通过 SSH 复制文件 | `scp file.txt user@host:/remote/path/` |
| `ssh` | 登录远程主机 | `ssh user@host` |

`user@host` 和 `/remote/path/` 需要替换为实际远程账户、主机和目录。连接需要相应的网络、认证和远程服务支持。

### 查看本机 8080 端口的 TCP 监听进程

```bash
sudo ss -ltnp 'sport = :8080'
```

`-p` 用于显示进程信息；查看其他用户的进程通常需要额外权限。没有匹配输出，通常表示当前未发现该端口的 TCP 监听项。

### 将 HTTP 响应保存为文件

以下命令会在当前目录创建或覆盖 `example.html`：

```bash
curl --fail --location --output example.html https://example.com/
```

- `--fail`：遇到部分 HTTP 错误状态时返回失败。
- `--location`：跟随重定向。
- `--output`：指定输出文件。

`ping` 不通不一定说明网站不可访问，目标可能禁用了 ICMP。排查网站时，还应检查 DNS、TCP 端口和 HTTP 响应。

## 6. 文本处理

以下文件示例使用第 1 节创建的练习目录。若已经切换目录，先在原终端中运行 `cd "$demo_dir"`。

| 命令 | 用途 | 示例 |
|---|---|---|
| `cat` | 输出文件内容 | `cat file.txt` |
| `tac` | 按行倒序输出 | `tac file.txt` |
| `more` | 分页查看文件 | `more app.log` |
| `less` | 可搜索、前后翻页查看文件 | `less app.log` |
| `head` | 查看前几行 | `head -n 5 app.log` |
| `tail` | 查看最后几行 | `tail -n 5 app.log` |
| `grep` | 按模式筛选行 | `grep -n -i "error" app.log` |
| `cut` | 按分隔符提取字段 | `cut -d ',' -f 1 data.csv` |
| `awk` | 按字段处理文本 | `awk -F ',' '{print $1}' data.csv` |
| `sed` | 对文本进行替换等处理 | `sed 's/old/new/g' file.txt` |

`grep` 默认区分大小写；`-i` 忽略大小写，`-n` 显示行号。没有匹配时通常返回状态码 1，不代表工具运行出错。

`cut` 和上述简单 `awk` 示例适合按逗号分隔的简单文本，不能可靠处理字段内带逗号、引号或换行的复杂 CSV。

### 查看持续追加的日志

```bash
tail -f app.log
```

按 `Ctrl+C` 停止跟踪。查看 `less` 或 `more` 时，通常按 `q` 退出；在 `less` 中输入 `/关键词` 可搜索，按 `n` 查找下一个。

### 替换文本并保留源文件

`sed` 默认输出到终端，不修改原文件。使用新的输出文件即可保留源文件：

```bash
sed 's/old/new/g' file.txt > replaced.txt
cat replaced.txt
```

不要把输出重定向到同一个输入文件，例如 `sed ... file.txt > file.txt`；Shell 可能先清空文件。

在 GNU sed 中，如需原地修改并保留备份，可使用：

```bash
sed -i.bak 's/old/new/g' file.txt
```

执行后，`file.txt` 是修改结果，`file.txt.bak` 保存本次修改前的内容。重复执行可能覆盖已有备份。

## 7. 系统状态

这些命令分别查看内核、负载、内存和 I/O。不同指标需要结合观察。

| 命令 | 用途 | 示例 |
|---|---|---|
| `uname` | 查看内核及系统相关信息 | `uname -a` |
| `uptime` | 查看运行时长和负载平均值 | `uptime` |
| `dmesg` | 查看内核消息，包括启动后的消息 | `sudo dmesg` |
| `free` | 查看内存和交换空间 | `free -h` |
| `vmstat` | 按间隔查看内存、调度和 I/O 等统计 | `vmstat 1 5` |
| `iostat` | 查看 CPU 与设备 I/O 统计 | `iostat -xz 1 5` |
| `sar` | 实时采样系统活动 | `sar 1 5` |

说明：

- `uptime` 常显示最近 1、5、15 分钟的负载平均值，它不是 CPU 使用率百分比。
- `free` 中的 `available` 通常比单看 `free` 列更适合估计可供新程序使用的内存。
- `vmstat`、`iostat` 的首份报告通常包含自启动以来的统计，后续报告反映采样间隔。
- `iostat`、`sar` 通常由 `sysstat` 软件包提供。
- `sar 1 5` 表示每秒采样一次，共五次；历史查询需要系统此前已启用数据采集。

查看发行版名称和版本：

```bash
cat /etc/os-release
```

## 8. 软件包管理

根据发行版选择对应工具。下面是不同系统的替代用法，不需要全部执行。

### Debian / Ubuntu：APT

更新软件包索引：

```bash
sudo apt update
```

升级已安装软件包；执行前查看拟议变更：

```bash
sudo apt upgrade
```

安装或卸载一个软件包，以 `tree` 为例：

```bash
sudo apt install tree
```

```bash
sudo apt remove tree
```

`apt update` 更新可用软件包信息，不会直接升级已安装软件。

### Fedora / RHEL 等：DNF

安装软件包：

```bash
sudo dnf install tree
```

升级软件包：

```bash
sudo dnf upgrade
```

部分旧版系统使用 `yum`，例如：

```bash
sudo yum install tree
```

工具、仓库和软件包名称会随发行版及版本变化。

### RPM：查询和处理 RPM 包

查询已安装的 RPM 包：

```bash
rpm -qa
```

安装本地 RPM 文件时，现代系统通常优先使用 DNF，让它解析依赖。以下 `package.rpm` 必须替换为实际文件：

```bash
sudo dnf install ./package.rpm
```

也可以使用底层命令 `sudo rpm -ivh package.rpm`，但它不会自动从仓库下载并解决依赖。

## 9. 计划任务与后台作业

临时后台运行、定时执行和长期服务管理对应不同需求。

| 命令 | 用途 | 示例 |
|---|---|---|
| `crontab` | 编辑当前用户的周期任务 | `crontab -e` |
| `crontab -l` | 查看当前用户的周期任务 | `crontab -l` |
| `at` | 安排一次性任务 | `at now + 5 minutes` |
| `nohup` | 让程序忽略挂断信号，常用于终端断开后继续运行 | 见下方完整练习 |

### cron：周期执行

运行 `crontab -e` 后，加入：

```cron
0 2 * * * /usr/bin/date >> /home/alice/daily-time.log 2>&1
```

含义：每天凌晨 2 点，将时间写入日志。

使用前，将 `/home/alice/` 换成实际用户目录，并使用 `command -v date` 确认程序路径。

五个时间字段依次为：

```text
分钟 小时 日 月 星期
```

cron 的环境变量和工作目录通常不同于交互式终端。脚本、输入文件和日志建议使用完整路径，并确认 cron 服务正在运行。

### at：一次性执行

先运行：

```bash
at now + 5 minutes
```

进入交互输入后，填写要执行的命令，最后按 `Ctrl+D` 提交。

`at` 通常需要额外安装，并依赖 `atd` 服务。可用 `atq` 查看排队任务，用 `atrm` 删除指定任务。

### 完整练习：nohup 后台运行并记录日志

下面在新的临时目录中运行一个约 30 秒的示例，不依赖前文文件。

```bash
nohup_demo_dir="$(mktemp -d)"
nohup bash -c 'date; sleep 30; date' \
  > "$nohup_demo_dir/run.log" 2>&1 < /dev/null &
nohup_demo_pid=$!
printf '后台进程 PID：%s\n' "$nohup_demo_pid"
printf '日志路径：%s\n' "$nohup_demo_dir/run.log"
```

其中：

- `>`：将标准输出写入日志。
- `2>&1`：将标准错误写入同一位置。
- `< /dev/null`：避免从当前终端读取输入。
- `&`：放入后台运行。

`nohup` 不提供失败重启或开机自启。长期服务通常应使用环境支持的服务管理器，例如 systemd。

## 10. 其他常用

| 命令 | 用途 | 示例 |
|---|---|---|
| `alias` | 定义当前 Shell 的命令别名 | `alias ll='ls -lah'` |
| `history` | 查看 Bash 命令历史 | `history` |
| `clear` | 清空终端显示 | `clear` |
| `echo` | 输出简单文本 | `echo "Hello, Linux!"` |
| `printf` | 按明确格式输出文本 | `printf '%s\n' "Hello, Linux!"` |
| `date` | 查看日期和时间 | `date '+%F %T %z'` |
| `cal` | 查看日历，可能需要安装 | `cal` |
| `man` | 查看命令手册 | `man ls` |

`alias` 直接定义后，通常只影响当前 Shell。需要长期使用时，可根据环境放入 `~/.bashrc` 等配置文件。

查看工具参数：

```bash
ls --help
```

查看 Shell 内置命令的帮助：

```bash
help cd
```

`--help` 是常见约定，并非所有命令都支持。

### 关机与重启

以下操作会中断当前系统上的任务和连接，应分别按需执行。

立即关机：

```bash
sudo shutdown -h now
```

立即重启：

```bash
sudo reboot
```

容器、WSL 和普通主机对此类命令的支持及影响范围可能不同。

## 练习建议

可以按下面的顺序学习：

1. 用 `pwd`、`ls`、`cd` 熟悉目录。
2. 在练习目录中使用 `mkdir`、`cp`、`mv`。
3. 用 `cat`、`less`、`grep` 阅读和筛选文本。
4. 用 `find` 按名称查找文件。
5. 用 `ps`、`top`、`free`、`df` 查看系统状态。
6. 理解管道与重定向后，再组合命令。

例如，下面这段代码使用第 1 节的练习日志，筛选包含 `error` 的行并保存结果：

```bash
cd "$demo_dir"
grep -i "error" app.log > errors.txt
cat errors.txt
```

`>` 会创建或覆盖文件，`>>` 会追加内容。管道 `|` 则将前一个命令的标准输出交给下一个命令。

文件练习可在独立目录中完成；涉及磁盘分区、系统权限和服务配置时，适合使用有快照的虚拟机。WSL 适合许多命令行练习，但设备和服务管理行为可能与普通 Linux 主机不同。

---

来源：[Linux常用命令 · 知识库](https://markstao.github.io/my-blog/article/Linux/Linux%E5%91%BD%E4%BB%A4.html)

本文在原有十类清单基础上补充使用条件、参数说明和练习示例。来源链接沿用原文，未逐项核验外部页面；命令示例未实机测试。
