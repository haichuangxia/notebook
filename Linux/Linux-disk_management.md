## 目录
- [目录](#目录)
- [磁盘挂载与卸载](#磁盘挂载与卸载)
  - [mount命令](#mount命令)
    - [基本语法](#基本语法)
  - [vfstype](#vfstype)
  - [options](#options)
  - [示例](#示例)
- [umount命令](#umount命令)
- [磁盘信息查询](#磁盘信息查询)
  - [查询磁盘空间使用信息-df](#查询磁盘空间使用信息-df)
    - [选项](#选项)
    - [示例](#示例-1)
  - [查询文件大小信息-du](#查询文件大小信息-du)
## 磁盘挂载与卸载

### mount命令

#### 基本语法

`mount [-t vfstype] [-o options] 设备文件名 挂载点`

### vfstype

文件系统格式，drvfs是wsl中挂载Windows的文件格式

### options

-a --all

### 示例

`sudo mount -t drvfs F: /mnt/f`

## umount命令

`sudo umount /mnt/f`
## 磁盘信息查询
### 查询磁盘空间使用信息-df
`df`命令用来查询磁盘空间的使用情况,其主要的参数有

#### 选项
| 选项 | 长格式           | 作用                                    | 示例                         |
| ---- | ---------------- | --------------------------------------- | ---------------------------- |
| -h   | --human-readable | 以GB,MB等人易读的方式显示磁盘的利用情况 | `df -h`-显示磁盘空间利用情况 |

#### 示例

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
rootfs          122G   77G   46G  63% /
none            122G   77G   46G  63% /dev
none            122G   77G   46G  63% /run
none            122G   77G   46G  63% /run/lock
none            122G   77G   46G  63% /run/shm
none            122G   77G   46G  63% /run/user
tmpfs           122G   77G   46G  63% /sys/fs/cgroup
C:\             101G   87G   15G  86% /mnt/c
D:\             122G   77G   46G  63% /mnt/d
H:              120G   65G   55G  55% /mnt/h
```

### 查询文件大小信息-du