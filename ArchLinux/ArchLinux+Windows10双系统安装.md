# ArchLinux+Windows10双系统安装

## 1.安装Windows10

1. 安装完Windows10后，右键“此电脑”，单击“管理(G)”，单击右侧树状图的“磁盘管理”选项
2. 选中需要开辟新空间的磁盘，右键弹出菜单，选择“压缩卷(H)…”
3. 单击“压缩(S)”，然后选择刚刚压缩的未分配的卷，右键弹出菜单，选择“新建简单卷(I)…”
   在“格式化分区”步骤中选择“不要格式化这个卷(D)”，其他全部默认，完成

## 2.安装ArchLinux

### 1.无线网络连接

如果你用的是有线网络，请直接跳过此章节

```bash
iwctl # 进入iwctl
```

进入后：

```bash
device list # 看看你的网卡叫什么名字
```

```bash
station wlan0 scan # wlan0是无线网卡名
```

```bash
station wlan0 get-networks # 查看已被扫描的无线网络
```

```bash
station wlan0 connect CMCC # CMCC是网络名
```

接下来输入密码后就连接成功了，输入`exit`退出

如果还不能联网输入下面的命令试试：

```bash
systemctl start dhcpcd
```

### 2.检测网络连接

```bash
ping www.baidu.com -c2
```

### 3.同步系统时间

```bash
timedatectl set-ntp true
```

可以使用`timedatectl status`检查服务状态

### 4.更换镜像源

禁用Reflector服务：

```bash
systemctl stop reflector.service
```

更换镜像源：

```bash
vim /etc/pacman.d/mirrorlist
```

### 5.格式化磁盘分区

使用`fdisk -l`来查看当前磁盘状况，记住**EFI System分区**以及刚刚新建的**Microsoft basic data分区**对应的路径

格式化Microsoft basic data分区：

```bash
mkfs.ext4 /dev/nvme0n1p5
```

### 6.挂载磁盘

挂载根目录：

```bash
mount /dev/nvme0n1p5 /mnt
```

挂载EFI分区：

```bash
mkdir -p /mnt/boot/EFI
mount /dev/nvme0n1p2 /mnt/boot/EFI
```

### 7.安装ArchLinux

安装必备的软件包：

```bash
pacstrap /mnt base linux linux-firmware
```

安装功能性软件：

```bash
pacstrap /mnt dhcpcd iwd vim sudo
```

### 8.配置ArchLinux

生成fstab文件：

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**强烈建议**使用`cat /mnt/etc/fstab`检查一下文件是否正确

进入新系统：

```bash
arch-chroot /mnt
```

设置时区：

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

同步硬件时钟：

```bash
hwclock --systohc
```

设置本地地址：

```bash
vim /etc/locale.gen
```

将`#en_US.UTF-8 UTF-8`的注释去掉

生成Locale信息：

```bash
locale-gen
```

接着往`locale.conf`输入一些内容：

```bash
echo 'LANG=en_US.UTF-8' > /etc/locale.conf
```

设置主机名：

```bash
echo YUXIANG-PC > /etc/hostname
```

设置Host：

```bash
vim /etc/hosts
```

```bash
127.0.0.1    localhost
::1		localhost
127.0.1.1	YUXIANG-PC.localdomain	YUXIANG-PC # 主机名.本地域名 主机名
```

设置Root用户密码：

```bash
passwd root
```

安装微码（根据自己的CPU型号选择）：

```bash
pacman -S intel-ucode # Intel的CPU
```

```bash
pacman -S amd-ucode # AMD的CPU
```

### 9.安装引导程序

安装必备包：

```bash
pacman -S grub efibootmgr
```

安装Grub：

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=GRUB
```

生成配置文件：

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

***

完毕，输入`exit`退回安装环境

使用`umount -R /mnt`卸载分区

输入`reboot`重启！**重启后要拔掉U盘！**

### 10.给新系统设置网络

> 以Root账户进入系统

设置dhcpcd开机自启：

```bash
systemctl enable dhcpcd
```

立即启动dhcpcd：

```bash
systemctl shart dhcpcd
```

再次生成`grub.cfg`配置文件，会自动在Grub引导界面加入Windows10启动选项

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

编辑`/boot/grub/grub.cfg`，设置开机启动等待时间

最后使用`ping`检测一下是否联网：

```bash
ping www.baidu.com -c2
```

#### 附：命令行查看系统信息

可以安装`neofetch`这个软件包来通过命令行查看系统信息：

```bash
pacman -S neofetch
```

```bash
neofetch
```
