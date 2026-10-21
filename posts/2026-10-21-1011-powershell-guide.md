---
title: PowerShell 用法与常见命令大全：从入门到自动化
date: 2026-10-21
time: 10:11
tag: 工具
minutes: 15
cover: images/2026-10-21-powershell-guide-01.jpg
excerpt: PowerShell 是微软推出的跨平台自动化与配置管理框架。它通过对象管道连接命令，适合处理文件、进程、服务、网络、脚本和远程管理任务。本文从基础语法开始，介绍常用命令、管道操作、脚本编写和自动化实践。
---

> 本文综合整理自七篇 PowerShell 教程：知乎《PowerShell快速入门》（菲儿曼）、腾讯云《花15分钟时间掌握必知必会的20个PowerShell命令》（Windows技术交流）、博客园《PowerShell 命令汇总(按功能)》（kyle-7Qc）、CSDN《PowerShell使用教程（挑战全网最全）》、《powershell命令大全（转载）》（知乎）、《一文了解PowerShell 基础指令和高级用法》（知乎）、《PowerShell 速查表》。示例均已按 PowerShell 5.1 / 7 语法校正。

![PowerShell 教程封面](images/2026-10-21-powershell-guide-01.jpg)

*图 1｜本文封面：PowerShell 主题插画——蓝色标志、终端窗口与自动化管理概念。*

# PowerShell 用法与常见命令大全：从入门到自动化

PowerShell 是微软推出的跨平台自动化与配置管理框架，包含命令行 Shell 和脚本语言。
它与传统 CMD 的主要区别是：PowerShell 命令之间传递的是对象，而不是普通文本。因此，我们可以直接对命令结果进行筛选、排序、格式化和导出。

本文适用于 Windows PowerShell 5.1 和 PowerShell 7。部分网络和系统管理命令只适用于 Windows，文中会单独说明。

## 一、PowerShell 是什么？

PowerShell 的核心特点包括：

- **命令采用"动词-名词"格式**，例如 `Get-Process`、`Get-Service`。
- **使用对象管道**，命令输出可以直接交给下一个命令处理。
- **支持脚本编程**，可以将多个命令保存为 `.ps1` 文件。
- **支持自动化管理**，适合批量处理文件、进程、服务和系统配置。
- **支持跨平台运行**，PowerShell 7 可以运行在 Windows、Linux 和 macOS 上。

例如：

```powershell
Get-Process
```

这个命令用于获取当前系统中的进程。

下面的命令会获取内存占用超过 100 MB 的进程，并只显示进程名称和内存占用：

```powershell
Get-Process |
    Where-Object { $_.WorkingSet64 -gt 100MB } |
    Select-Object ProcessName, Id, WorkingSet64
```

其中：

- `Get-Process` 获取进程对象。
- `Where-Object` 筛选对象。
- `$_` 表示管道中的当前对象。
- `Select-Object` 选择需要显示的属性。

## 二、如何启动 PowerShell

### Windows

可以通过以下方式启动 PowerShell：

1. 在开始菜单中搜索"PowerShell"。
2. 按下 `Win + R`，输入 `powershell`，然后回车。
3. 在文件资源管理器的地址栏中输入 `powershell`，从当前目录打开 PowerShell。
4. 右键开始菜单，选择"终端"或"Windows PowerShell"。
5. 需要管理员权限时，选择"以管理员身份运行"。

### Linux 和 macOS

安装 PowerShell 7 后，在终端执行：

```bash
pwsh
```

退出 PowerShell：

```powershell
exit
```

涉及修改执行策略、安装模块、管理服务或配置远程管理时，通常需要管理员权限。

## 三、基础语法

### 1. 动词-名词命令

PowerShell 命令通常采用以下格式：

```text
动词-名词
```

常见动词包括：

| 动词 | 含义 | 示例 |
|---|---|---|
| `Get` | 获取 | `Get-Process` |
| `Set` | 设置 | `Set-Location` |
| `New` | 创建 | `New-Item` |
| `Remove` | 删除 | `Remove-Item` |
| `Start` | 启动 | `Start-Service` |
| `Stop` | 停止 | `Stop-Process` |
| `Restart` | 重启 | `Restart-Service` |
| `Export` | 导出 | `Export-Csv` |
| `ConvertTo` | 转换为 | `ConvertTo-Json` |

示例：

```powershell
Get-Process -Name notepad
Get-Service -Name wuauserv
```

### 2. 变量

变量以 `$` 开头：

```powershell
$message = "Hello, PowerShell"
$count = 100
$isEnabled = $true
Write-Output $message
Write-Output $count
```

PowerShell 变量不要求提前声明类型：

```powershell
$name = "Alice"
$age = 30
$roles = @("Admin", "User")
```

如果需要查看变量类型，可以使用：

```powershell
$name.GetType()
```

### 3. 注释

单行注释使用 `#`：

```powershell
# 获取当前日期和时间
Get-Date
```

多行注释使用 `<#` 和 `#>`：

```powershell
<#
这是一段多行注释。
可以用来说明脚本的用途和使用方法。
#>
```

### 4. 常用别名

PowerShell 提供了一些适合交互式使用的别名：

| 别名 | 完整命令 | 用途 |
|---|---|---|
| `ls`、`dir` | `Get-ChildItem` | 列出目录内容 |
| `pwd` | `Get-Location` | 查看当前路径 |
| `cd` | `Set-Location` | 切换目录 |
| `cls`、`clear` | `Clear-Host` | 清屏 |
| `cat` | `Get-Content` | 读取文件 |
| `ps` | `Get-Process` | 查看进程 |

可以使用以下命令查看别名：

```powershell
Get-Alias
```

在脚本和文档中，建议优先使用完整命令名，这样更容易阅读，也更适合跨环境使用。

## 四、文件与目录操作

### 1. 查看目录内容

```powershell
Get-ChildItem
```

查看指定目录：

```powershell
Get-ChildItem -Path "C:\Windows"
```

显示隐藏项目：

```powershell
Get-ChildItem -Force
```

递归查看所有子目录：

```powershell
Get-ChildItem -Path "C:\Logs" -Recurse
```

### 2. 筛选文件

查找扩展名为 `.log` 的文件：

```powershell
Get-ChildItem -Path "C:\Logs" -Filter "*.log"
```

排除文件名中包含 `error` 的日志：

```powershell
Get-ChildItem -Path "C:\Logs" -Filter "*.log" |
    Where-Object { $_.Name -notlike "*error*" }
```

按文件大小筛选：

```powershell
Get-ChildItem -Path "C:\Logs" -File |
    Where-Object { $_.Length -gt 10MB }
```

### 3. 创建文件和目录

创建目录：

```powershell
New-Item -Path "C:\Temp\NewFolder" -ItemType Directory
```

创建文件：

```powershell
New-Item -Path "C:\Temp\test.txt" -ItemType File
```

如果目标路径可能已经存在，可以使用 `-Force`：

```powershell
New-Item -Path "C:\Temp\test.txt" -ItemType File -Force
```

### 4. 复制、移动和重命名

复制文件：

```powershell
Copy-Item `
    -Path "C:\Temp\test.txt" `
    -Destination "C:\Temp\Backup\test.txt"
```

复制整个目录：

```powershell
Copy-Item `
    -Path "C:\Temp\Source" `
    -Destination "C:\Temp\Backup" `
    -Recurse
```

移动文件：

```powershell
Move-Item `
    -Path "C:\Temp\test.txt" `
    -Destination "C:\Temp\Backup\test.txt"
```

重命名文件：

```powershell
Rename-Item `
    -Path "C:\Temp\old.txt" `
    -NewName "new.txt"
```

反引号 `` ` `` 用于换行。也可以将命令写在一行中。

### 5. 删除文件和目录

删除文件：

```powershell
Remove-Item -Path "C:\Temp\test.txt"
```

删除目录及其全部内容：

```powershell
Remove-Item -Path "C:\Temp\OldFolder" -Recurse
```

删除操作通常不可逆。执行批量删除前，建议先使用 `-WhatIf` 查看将要执行的操作：

```powershell
Remove-Item -Path "C:\Temp\OldFolder" -Recurse -WhatIf
```

确认无误后，再去掉 `-WhatIf`。

### 6. 判断路径是否存在

```powershell
Test-Path -Path "C:\Windows"
```

返回结果为 `$true` 或 `$false`。

结合条件判断：

```powershell
$path = "C:\Temp\test.txt"
if (Test-Path -Path $path) {
    Write-Output "文件存在"
}
else {
    Write-Output "文件不存在"
}
```

### 7. 读取和写入文件

读取文件：

```powershell
Get-Content -Path "C:\Temp\test.txt"
```

覆盖写入：

```powershell
Set-Content -Path "C:\Temp\test.txt" -Value "新的文件内容"
```

追加内容：

```powershell
Add-Content -Path "C:\Temp\test.txt" -Value "追加的一行内容"
```

搜索日志关键词：

```powershell
Get-Content -Path "C:\Logs\app.log" |
    Select-String -Pattern "error"
```

在 PowerShell 7 中，可以指定 UTF-8 编码：

```powershell
Set-Content `
    -Path "C:\Temp\test.txt" `
    -Value "中文内容" `
    -Encoding utf8
```

## 五、进程、服务和系统信息

### 1. 查看和筛选进程

查看所有进程：

```powershell
Get-Process
```

查看指定进程：

```powershell
Get-Process -Name notepad
```

查看内存占用较高的进程：

```powershell
Get-Process |
    Sort-Object -Property WorkingSet64 -Descending |
    Select-Object -First 10 ProcessName, Id, WorkingSet64
```

结束指定进程：

```powershell
Stop-Process -Name notepad
```

结束进程前，可以先查看将要匹配的对象：

```powershell
Get-Process -Name notepad
```

停止进程可能导致未保存的数据丢失，请谨慎使用。

### 2. 查看和管理服务

查看所有服务：

```powershell
Get-Service
```

查看指定服务：

```powershell
Get-Service -Name wuauserv
```

启动服务：

```powershell
Start-Service -Name wuauserv
```

停止服务：

```powershell
Stop-Service -Name wuauserv
```

重启服务：

```powershell
Restart-Service -Name wuauserv
```

查看正在运行的服务：

```powershell
Get-Service |
    Where-Object { $_.Status -eq "Running" }
```

管理系统服务通常需要管理员权限。

### 3. 查看系统信息

获取当前日期和时间：

```powershell
Get-Date
```

查看当前路径：

```powershell
Get-Location
```

查看计算机综合信息：

```powershell
Get-ComputerInfo
```

查看 PowerShell 版本：

```powershell
$PSVersionTable
```

其中：

```powershell
$PSVersionTable.PSVersion
```

可以单独查看 PowerShell 版本号。

## 六、网络相关命令

### 1. 测试网络连通性

```powershell
Test-Connection -ComputerName "www.example.com" -Count 4
```

该命令类似于传统的 `ping`。

### 2. 查看本机 IP 地址

在 Windows PowerShell 和 PowerShell 7 的 Windows 环境中，可以使用：

```powershell
Get-NetIPAddress
```

只查看 IPv4 地址：

```powershell
Get-NetIPAddress |
    Where-Object { $_.AddressFamily -eq "IPv4" }
```

这些 `Get-Net*` 命令主要适用于 Windows。

### 3. 查看端口监听状态

查看所有监听端口：

```powershell
Get-NetTCPConnection -State Listen
```

查看本机 8080 端口：

```powershell
Get-NetTCPConnection -State Listen |
    Where-Object { $_.LocalPort -eq 8080 }
```

如果系统中没有 `Get-NetTCPConnection`，可以使用传统命令：

```powershell
netstat -ano
```

### 4. DNS 查询

```powershell
Resolve-DnsName -Name example.com
```

`Resolve-DnsName` 主要适用于 Windows PowerShell 和 PowerShell 7 的 Windows 环境。

## 七、管道、筛选、排序和导出

### 1. 使用管道

管道符号是 `|`，用于把前一个命令的输出传递给下一个命令。

```powershell
Get-Process |
    Where-Object { $_.WorkingSet64 -gt 100MB } |
    Select-Object ProcessName, Id, WorkingSet64
```

### 2. 筛选对象

筛选正在运行的服务：

```powershell
Get-Service |
    Where-Object { $_.Status -eq "Running" }
```

筛选名称以 `p` 开头的进程：

```powershell
Get-Process |
    Where-Object { $_.ProcessName -like "p*" }
```

### 3. 排序对象

按 CPU 使用时间从高到低排序：

```powershell
Get-Process |
    Sort-Object -Property CPU -Descending
```

查看 CPU 使用时间最高的前五个进程：

```powershell
Get-Process |
    Sort-Object -Property CPU -Descending |
    Select-Object -First 5 ProcessName, Id, CPU
```

### 4. 选择属性

```powershell
Get-Process |
    Select-Object ProcessName, Id, CPU, WorkingSet64
```

### 5. 统计数量

```powershell
$serviceCount = (Get-Service).Count
Write-Output "系统中共有 $serviceCount 个服务"
```

### 6. 导出 CSV

```powershell
Get-Process |
    Select-Object ProcessName, Id, CPU, WorkingSet64 |
    Export-Csv -Path ".\processes.csv" -NoTypeInformation
```

### 7. 导出 HTML

```powershell
Get-Process |
    Select-Object ProcessName, Id, CPU, WorkingSet64 |
    ConvertTo-Html |
    Set-Content -Path ".\processes.html"
```

生成的 `processes.html` 可以使用浏览器打开。

## 八、编写和执行 PowerShell 脚本

将命令保存为 `.ps1` 文件，就可以重复执行。

创建 `test.ps1`：

```powershell
# 获取当前日期
Get-Date
# 统计系统服务数量
$serviceCount = (Get-Service).Count
Write-Output "系统中共有 $serviceCount 个服务"
```

进入脚本所在目录：

```powershell
Set-Location -Path "C:\Scripts"
```

执行脚本：

```powershell
.\test.ps1
```

脚本路径前的 `.\` 表示"当前目录"。

### 执行策略

查看当前执行策略：

```powershell
Get-ExecutionPolicy -List
```

如果需要允许当前用户运行本地创建的脚本，可以设置：

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

也可以只对当前 PowerShell 会话临时设置：

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

执行策略会影响脚本运行。不要为了运行来源不明的脚本而随意降低系统安全限制。

## 九、流程控制和自动化

### 1. 哈希表

哈希表用于保存键和值：

```powershell
$config = @{
    Server  = "localhost"
    Port    = 8080
    Enabled = $true
}
Write-Output "服务器地址：$($config.Server)"
Write-Output "服务器端口：$($config.Port)"
```

### 2. 自定义对象

可以使用 `[PSCustomObject]` 创建结构化对象：

```powershell
$user = [PSCustomObject]@{
    Name  = "Alice"
    Age   = 30
    Roles = @("Admin", "User")
}
$user |
    Format-Table -AutoSize
```

### 3. 条件判断

```powershell
$cpuUsage = 75
if ($cpuUsage -gt 80) {
    Write-Host "CPU 使用率过高！" -ForegroundColor Red
}
elseif ($cpuUsage -gt 60) {
    Write-Host "CPU 使用率偏高。" -ForegroundColor Yellow
}
else {
    Write-Host "CPU 使用率正常。" -ForegroundColor Green
}
```

### 4. 循环

使用 `for` 循环：

```powershell
for ($i = 1; $i -le 5; $i++) {
    Write-Output "迭代次数：$i"
}
```

遍历进程：

```powershell
Get-Process -Name chrome, notepad -ErrorAction SilentlyContinue |
    ForEach-Object {
        Write-Output "进程：$($_.ProcessName)，ID：$($_.Id)"
    }
```

如果指定的进程不存在，`-ErrorAction SilentlyContinue` 可以避免输出错误信息。

### 5. 错误处理

推荐使用 `try/catch/finally` 处理需要捕获的错误：

```powershell
try {
    $file = Get-Item -Path "C:\Temp\not-found.txt" -ErrorAction Stop
    Write-Output "文件大小：$($file.Length)"
}
catch {
    Write-Error "读取文件失败：$($_.Exception.Message)"
}
finally {
    Write-Output "操作结束"
}
```

`-ErrorAction Stop` 会将非终止错误转换为可以被 `catch` 捕获的错误。

### 6. JSON 处理

将 JSON 字符串转换为对象：

```powershell
$json = '{"Name":"Bob","Age":25}'
$user = $json | ConvertFrom-Json
Write-Output $user.Name
Write-Output $user.Age
```

修改对象并保存为 JSON 文件：

```powershell
$user.Age = 26
$user |
    ConvertTo-Json |
    Set-Content -Path ".\data.json" -Encoding utf8
```

如果对象包含嵌套数据，可以使用 `-Depth`：

```powershell
$user |
    ConvertTo-Json -Depth 5 |
    Set-Content -Path ".\data.json" -Encoding utf8
```

## 十、远程管理

PowerShell 支持通过 PowerShell Remoting 在远程计算机上执行命令。

### 1. 启用 PowerShell Remoting

在目标 Windows 计算机上，以管理员身份执行：

```powershell
Enable-PSRemoting
```

实际使用时，目标计算机需要满足以下条件：

- 已启用 WinRM。
- 当前账户具有远程管理权限。
- 网络和防火墙允许远程连接。
- 客户端与目标计算机之间能够互相解析和访问。
- 工作组环境可能需要额外配置 TrustedHosts。

### 2. 进入远程交互会话

```powershell
Enter-PSSession -ComputerName "RemotePC"
```

退出远程会话：

```powershell
Exit-PSSession
```

### 3. 远程执行命令

```powershell
Invoke-Command `
    -ComputerName "RemotePC" `
    -ScriptBlock {
        Get-Process
    }
```

也可以在多台计算机上执行：

```powershell
$computers = @("PC01", "PC02", "PC03")
Invoke-Command `
    -ComputerName $computers `
    -ScriptBlock {
        Get-Date
        Get-ComputerInfo
    }
```

远程管理涉及权限和网络配置，建议先在测试计算机上验证。

## 十一、常用命令速查表

| 用途 | 推荐命令 |
|---|---|
| 列出目录 | `Get-ChildItem` |
| 查看当前路径 | `Get-Location` |
| 切换目录 | `Set-Location` |
| 创建文件或目录 | `New-Item` |
| 复制 | `Copy-Item` |
| 移动 | `Move-Item` |
| 重命名 | `Rename-Item` |
| 删除 | `Remove-Item` |
| 判断路径 | `Test-Path` |
| 读取文件 | `Get-Content` |
| 覆盖写入 | `Set-Content` |
| 追加写入 | `Add-Content` |
| 搜索文本 | `Select-String` |
| 查看进程 | `Get-Process` |
| 停止进程 | `Stop-Process` |
| 查看服务 | `Get-Service` |
| 启动服务 | `Start-Service` |
| 停止服务 | `Stop-Service` |
| 重启服务 | `Restart-Service` |
| 查看系统信息 | `Get-ComputerInfo` |
| 查看日期时间 | `Get-Date` |
| 测试网络 | `Test-Connection` |
| 查看 IP | `Get-NetIPAddress` |
| 查看监听端口 | `Get-NetTCPConnection` |
| 筛选对象 | `Where-Object` |
| 选择属性 | `Select-Object` |
| 排序对象 | `Sort-Object` |
| 遍历对象 | `ForEach-Object` |
| 导出 CSV | `Export-Csv` |
| 转换为 HTML | `ConvertTo-Html` |
| 转换为 JSON | `ConvertTo-Json` |
| 解析 JSON | `ConvertFrom-Json` |
| 查看帮助 | `Get-Help` |
| 查找命令 | `Get-Command` |
| 清屏 | `Clear-Host` |
| 退出 PowerShell | `exit` |

## 十二、如何查找命令和帮助

查找与进程相关的命令：

```powershell
Get-Command -Noun Process
```

查找名称中包含"服务"的命令：

```powershell
Get-Command *Service*
```

查看命令帮助：

```powershell
Get-Help Get-Process
```

查看完整帮助：

```powershell
Get-Help Get-Process -Full
```

查看命令示例：

```powershell
Get-Help Get-Process -Examples
```

在线查看最新帮助：

```powershell
Update-Help
```

某些系统需要以管理员身份运行 `Update-Help`。

## 十三、适合初学者的学习路径

可以按照下面的顺序学习：

1. 了解 `Get-Command` 和 `Get-Help`。
2. 掌握 `Get-ChildItem`、`Get-Content` 和 `Set-Location`。
3. 学习变量、条件判断和循环。
4. 掌握管道、`Where-Object`、`Select-Object` 和 `Sort-Object`。
5. 学习进程、服务、网络和系统信息命令。
6. 将常用命令保存为 `.ps1` 脚本。
7. 学习错误处理、JSON 和 CSV。
8. 最后再学习远程管理和批量自动化。

PowerShell 的核心思路可以概括为：

```text
获取对象 → 筛选对象 → 选择属性 → 排序或转换 → 导出结果
```

例如：

```powershell
Get-Process |
    Where-Object { $_.WorkingSet64 -gt 100MB } |
    Select-Object ProcessName, Id, WorkingSet64 |
    Sort-Object -Property WorkingSet64 -Descending |
    Export-Csv -Path ".\large-processes.csv" -NoTypeInformation
```

掌握命令命名规则、帮助系统和对象管道后，就可以逐步把重复性的系统操作转换为可复用的自动化脚本。
