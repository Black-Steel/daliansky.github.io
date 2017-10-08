---
title: Windows下使用diskpart建立EFI分区及挂载EFI分区
urlname: Under-Windows-using-DISKPART-to-create-EFI-points
date: 2017-08-29 16:04:22
categories:
- 教程
tags:
- diskpart
- Windows
- 分区
- EFI
- ESP
---

### Windows下使用diskpart建立EFI分区及挂载EFI分区
很多人在安装黑苹果的时候都需要在`Windows`下使用[DiskGenius](www.diskgenius.cn/download.php)进行分区，其实在`Windows 7`及之后的版本本身就包含了更实用的磁盘分区命令，这个命令就是：`diskpart`

#### 创建EFI分区
> 以下操作假设磁盘未分区没有其它数据

```sh
diskpart
list disk       # 磁盘列表
select disk n   # 选择需要制作EFI分区的磁盘，n为磁盘号
create partition efi size=300   # efi分区大小为300Mb
```

#### 挂载EFI分区

```sh
diskpart
list disk           # 磁盘列表
select disk n       # 选择需要制作EFI分区的磁盘，n为磁盘号
list partition      # 磁盘分区列表
select partition n  # 选择efi分区，n为efi分区号
assign letter=X     # x为efi分区盘符
```
打开资源管理器，会出现一个盘符为X的磁盘，格式化为fat32格式
以后再操作EFI分区请重复输入上述命令

### QQ群:
331686786 [一起黑苹果](http://shang.qq.com/wpa/qunwpa?idkey=db511a29e856f37cbb871108ffa77a6e79dde47e491b8f2c8d8fe4d3c310de91)
