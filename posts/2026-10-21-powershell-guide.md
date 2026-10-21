---
title: PowerShell 用法与常见命令大全｜从入门到自动化
date: 2026-10-21
time: 10:06
tag: 工具
minutes: 15
cover: images/2026-10-21-powershell-guide-01.jpg
excerpt: PowerShell 是微软推出的跨平台自动化与配置管理框架，包含命令行 Shell 与脚本语言，对象化的管道让批量管理 Windows 系统事半功倍。本文从启动方式、基础语法讲到文件、进程、网络、脚本与远程管理，附速查总表。
---

> 本文综合整理自七篇 PowerShell 教程：知乎《PowerShell快速入门》（菲儿曼）、腾讯云《花15分钟时间掌握必知必会的20个PowerShell命令》（Windows技术交流）、博客园《PowerShell 命令汇总(按功能)》（kyle-7Qc）、CSDN《PowerShell使用教程（挑战全网最全）》、《powershell命令大全（转载）》（知乎）、《一文了解PowerShell 基础指令和高级用法》（知乎）、《PowerShell 速查表》。示例均已按 PowerShell 5.1 / 7 语法校正。

![PowerShell 教程封面（AI 生成插画）](images/2026-10-21-powershell-guide-01.jpg)

*图 1｜本文封面：深蓝背景中的 PowerShell 白色波浪标志与终端面板（AI 生成）。*

## 一、PowerShell 是什么？

**PowerShell** 是微软开发的一套跨平台**自动化和配置管理框架**，包含一个命令行 Shell 与脚本语言。它构建在 .NET 之上，核心能力是：

- **命令（Cmdlet）**：统一采用"动词-名词"命名，如 `Get-Process`；
- **对象管道**：命令之间传递的是对象而非文本，配合 `Where-Object`、`Select-Object` 可以像搭积木一样组合出强大操作；
- **脚本化**：`.ps1` 脚本可批量执行、定时运行、自动化运维；
- **跨平台**：PowerShell 7 原生支持 Windows / Linux / macOS（Linux 上用 `pwsh` 启动）。

一句话：**CMD 是基础终端，PowerShell 是能自动化的高级终端**——而且它向下兼容大部分 CMD 命令。

## 二、如何启动 PowerShell

```text
Windows：
1. 开始菜单搜索 "PowerShell"（或 Windows PowerShell ISE——集成脚本环境，带编辑器）
2. Win + R → 输入 powershell 回车
3. 在资源管理器地址栏输入 powershell 回车（自动定位当前目录）
4. 右键开始菜单 → Windows PowerShell（管理员）

Linux / macOS：
安装后终端输入 pwsh 即可
```

> 需要管理员权限的操作（改执行策略、装模块、管理服务），请**以管理员身份运行**。

## 三、基础语法：动词-名词与变量

PowerShell 命令统一为 `动词-名词` 格式，参数以 `-` 开头：

```powershell
Get-Process -Name "notepad"   # 获取记事本进程
Get-Service -Name "wuauserv"  # 查看 Windows 更新服务
```

**变量**以 `$` 开头，可存字符串、数字、对象：

```powershell
$var = "Hello, PowerShell"
$num = 100
Write-Host $var              # 输出变量
```

**注释**用 `#`：

```powershell
# 获取当前系统日期
Get-Date
```

**常用别名**（对齐 Linux / CMD 习惯）：`ls`/`dir` 列目录、`pwd`/`gl` 当前路径、`cd`/`sl` 切换路径、`cls`/`clear` 清屏、`cat`/`more` 看文件、`ps` 进程。可用 `Get-Alias` 查看全部。

## 四、文件与目录操作

```powershell
Get-ChildItem                       # 列出目录内容（ls / dir）
Get-ChildItem C:\Windows            # 列出指定目录
Get-ChildItem "C:\Logs" -Include *.log -Exclude *error*.log  # 过滤

New-Item -Path "C:\NewFolder" -ItemType Directory   # 新建文件夹
New-Item -Path "C:\Temp\test.txt" -ItemType File    # 新建文件

Copy-Item -Path "a.txt" -Destination "backup\a.txt" # 复制
Move-Item -Path "a.txt" -Destination "D:\backup"    # 移动 / 重命名
Rename-Item old.txt new.txt                         # 重命名
Remove-Item "C:\Temp\test.txt"                      # 删除文件
Remove-Item "C:\OldFolder" -Recurse                 # 删除文件夹（递归）

Test-Path "C:\Windows"              # 判断路径是否存在
Get-Content "C:\Temp\test.txt"      # 读取文件内容
Set-Content test.txt "新内容"        # 覆盖写入
Add-Content test.txt "追加内容"      # 追加写入
Get-Content log.txt | Select-String "error"   # 搜索日志关键词
```

## 五、系统信息与进程服务

```powershell
Get-Process                 # 进程列表（ps / gps）
Get-Process -Name "chrome"  # 指定进程详情
Get-Process | Where-Object {$_.WorkingSet -gt 100MB}  # 筛选高内存进程
Stop-Process -Name "notepad"   # 结束进程（taskkill 的升级版）

Get-Service                 # 服务列表
Get-Service -Name "wuauserv" # 查看指定服务状态
Restart-Service -Name "wuauserv"   # 重启服务
Get-ComputerInfo            # 计算机综合信息（系统版本、处理器）
Get-Date                    # 当前日期时间
Get-Location                # 当前路径（pwd）
```

## 六、网络相关

```powershell
Test-Connection -ComputerName "www.example.com"   # 测试连通性（ping）
Get-NetIPAddress | Where-Object AddressFamily -eq "IPv4"  # 本机 IPv4
Get-NetTCPConnection -State Listen | Where-Object {$_.LocalPort -eq 8080}  # 查端口占用
Resolve-DnsName baidu.com   # DNS 解析（nslookup）
```

## 七、管道与对象（PowerShell 的灵魂）

管道 `|` 把前一个命令输出的**对象**交给后一个命令处理：

```powershell
# 筛选高内存进程并只看两个字段
Get-Process | Where-Object {$_.WorkingSet -gt 100MB} | Select-Object Path, WorkingSet

# 排序
Get-Process | Sort-Object CPU -Descending

# 按 CPU 占用取前 5
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5

# 导出：结果转成网页 / CSV，用浏览器或 Excel 分析
Get-Process | ConvertTo-Html > currentpss.html
Get-Process | Export-Csv currentpss.csv

# 计数
$serviceCount = (Get-Service).Count
Write-Host "系统中共有 $serviceCount 个服务"
```

## 八、脚本编写与执行

把命令存成 `.ps1` 文件即可复用：

```powershell
# test.ps1
Get-Date
$serviceCount = (Get-Service).Count
Write-Host "系统中共有 $serviceCount 个服务"
```

首次运行需放开执行策略（管理员）：

```powershell
Set-ExecutionPolicy RemoteSigned   # 允许运行本地创建的脚本
```

运行脚本（必须带 `.\`）：

```powershell
cd "C:\Scripts"
.\test.ps1
```

## 九、进阶：流程控制与自动化

**哈希表与自定义对象：**

```powershell
$config = @{ Server = "localhost"; Port = 8080; Enabled = $true }
Write-Host "服务器地址：$($config.Server)"

$user = [PSCustomObject]@{ Name = "Alice"; Age = 30; Roles = @("Admin","User") }
$user | Format-Table -AutoSize
```

**条件与循环：**

```powershell
$cpuUsage = (Get-Counter "\Processor(_Total)\% Processor Time").CounterSamples.CookedValue
if ($cpuUsage -gt 80) {
    Write-Host "CPU 使用率过高！" -ForegroundColor Red
} else {
    Write-Host "CPU 使用率正常。" -ForegroundColor Green
}

for ($i = 1; $i -le 5; $i++) { Write-Host "迭代次数：$i" }
Get-Process chrome, notepad | ForEach-Object {
    Write-Host "进程：$($_.Name)，ID：$($_.Id)"
}
```

**错误处理：**

```powershell
Get-Item "C:\NonExistent.txt" -ErrorAction SilentlyContinue  # 忽略错误
if ($Error.Count -gt 0) {
    Write-Host "错误：$($Error[0].Exception.Message)"
    $Error.Clear()
}
```

**JSON 处理：**

```powershell
$obj = '{"Name":"Bob","Age":25}' | ConvertFrom-Json
$obj.Age = 26
$obj | ConvertTo-Json | Set-Content "data.json"
```

## 十、远程管理

```powershell
Enable-PSRemoting                # 目标机开启远程管理（管理员，自动配置 WinRM）

Enter-PSSession -ComputerName "RemotePC"   # 进入远程交互会话
Invoke-Command -ComputerName "RemotePC" -ScriptBlock {Get-Process}  # 远程执行命令
```

## 速查总表

| 目的 | 命令 |
|---|---|
| 列目录 | `Get-ChildItem` / `ls` / `dir` |
| 当前路径 | `Get-Location` / `pwd` |
| 切换路径 | `Set-Location` / `cd` |
| 新建文件/夹 | `New-Item -ItemType File/Directory` |
| 复制/移动/重命名 | `Copy-Item` / `Move-Item` / `Rename-Item` |
| 删除 | `Remove-Item`（删文件夹加 `-Recurse`） |
| 读写文件 | `Get-Content` / `Set-Content` / `Add-Content` |
| 搜索文本 | `Select-String` |
| 进程 | `Get-Process` / `Stop-Process` |
| 服务 | `Get-Service` / `Restart-Service` |
| 系统信息 | `Get-ComputerInfo` / `Get-Date` |
| 连通性 | `Test-Connection` |
| IP/端口 | `Get-NetIPAddress` / `Get-NetTCPConnection` |
| 筛选 | `Where-Object`（`$_` 表示当前对象） |
| 选取/排序 | `Select-Object` / `Sort-Object` |
| 导出 | `Export-Csv` / `ConvertTo-Html` |
| 帮助 | `Get-Help 命令` / `Get-Command` |
| 清屏/退出 | `Clear-Host` / `exit` |

## 结语

PowerShell 的学习路径很清晰：**记住"动词-名词"语法 → 会用 `Get-Command` / `Get-Help` 查命令 → 熟练管道 + `Where-Object` 筛选 → 把常用操作写成 `.ps1` 脚本**。它比 CMD 强在"对象化 + 可编程"：一次操作的结果可以直接喂给下一个命令，批量改文件、查端口、导报表都只需一两行。系统管理员和开发者的日常自动化，多半都能靠它完成。

---

*本文整理自知乎《PowerShell快速入门》、《powershell命令大全（转载）》、《一文了解PowerShell 基础指令和高级用法》、腾讯云《必知必会的20个PowerShell命令》、博客园《PowerShell 命令汇总(按功能)》、CSDN《PowerShell使用教程（挑战全网最全）》、《PowerShell 速查表》七篇文章，版权归原作者所有；封面为 AI 生成插画。*
