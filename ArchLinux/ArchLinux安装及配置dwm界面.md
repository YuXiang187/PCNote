# ArchLinux安装及配置dwm界面

## 1.使用ArchLinuxCN源

> 以root用户登录

```bash
pacman -Syyu
vim /etc/pacman.conf
```

按下<kbd>/</kbd>输入Color，定位到`#Color`，**删掉**前面的`#`号

按下<kbd>Shift</kbd>+<kbd>G</kbd>到达文档末

光标往上，定位到下面一行，然后删除下面文字前面的`#`号注释

```bash
[multilib]
Include = /etc/pacman.d/mirrorlist
```

再把下面文字的注释去掉，如：

```bash
[custom]
SigLevel = Optional TrustAll
Server = file:///home/custompkgs
```

再把`[custom]`改成`[archlinuxcn]`，删除`SigLevel`一行，并更换为中科大的源

```bash
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

按下<kbd>Esc</kbd>输入`:wq`保存退出

退出后再更新一下新添加的内容

```bash
pacman -Syyu
pacman -S archlinuxcn-keyring
```

## 2.安装X窗系统、字体

X窗系统及其他：

```bash
pacman -S xorg-server xorg-xinit
pacman -S base-devel feh picom
pacman -S git unzip
pacman -S alsa-utils
pacman -S yay
```

字体：

```bash
pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
pacman -S ttf-nerd-fonts-symbols
```

## 3.创建新用户

创建用户：

```bash
useradd -m -g users -G wheel -s /bin/bash yuxiang
```

设置密码：

```bash
passwd yuxiang
```

编辑新用户权限：

```bash
EDITOR=vim visudo
```

按下<kbd>/</kbd>搜索`wheel`，然后<kbd>Enter</kbd>

定位到`# %wheel ALL=(ALL) ALL`，在`#`号下按**2**次<kbd>X</kbd>删除注释

按<kbd>Esc</kbd>输入`:wq`退出Vim

输入`exit`返回登录界面，使用刚创建的账户登录

## 4.安装dwm并配置工作环境

这里要连接外部U盘复制一些东西

将Windows下的`msyh.ttf`、`msyhbd.ttf`、`simhei.ttf`字体文件复制到`/usr/share/fonts`目录下

终端输入`alsamixer`进入调音台，在“Master”选项卡下按下<kbd>↑</kbd>键调节音量，然后按下<kbd>M</kbd>键取消静音

将已经写好的suckless系列软件的源代码复制到Downloads目录下，然后切换到源代码目录执行下面的命令安装：

```bash
make && sudo make clean install
```

安装顺序为`dwm` > `dmenu` > `slstatus` > `st`

复制一份xinitrc配置文件到家目录并编辑：

```bash
cp /etc/X11/xinit/xinitrc ~/.xinitrc
vim ~/.xinitrc
```

在最后显示`fi`段后插入（`fi`后面还有东西就删掉）：

```bash
......
	unset f
fi # 下面插入！如果下面有东西就删掉！

exec picom -CGb &
exec slstatus &
exec dwm
```

输入`sudo reboot`重启

启动dwm：

```bash
startx
```

安装浏览器：

```bash
yay -S google-chrome
```

配置~/.bashrc：

```bash
vim ~/.bashrc
```

```bash
alias l='lsblk'
alias n='neofetch'
alias e='exit'
alias u='sudo umount -R /mnt'
alias P='sudo poweroff'
alias R='sudo reboot'
alias S='sudo systemctl hybrid-sleep'
alias U='sudo pacman -Syyu'
```

安装fcitx输入法：

```bash
sudo pacman -S fcitx5-im fcitx5-configtool
```

安装fcitx提供的中文输入包：

```bash
sudo pacman -S fcitx5-chinese-addons
```

安装主题皮肤：

```bash
sudo pacman -S fcitx5-material-color
```

设置环境变量，让其它程序也能使用这个中文输入法：

```bash
vim ~/.pam_environment
```

按<kbd>I</kbd>输入：

```bash
INPUT_METHOD DEFAULT=fcitx5
GTK_IM_MODULE DEFAULT=fcitx5
QT_IM_MODULE DEFAULT=fcitx5
XMODIFIERS DEFAULT=\@im=fcitx5
```

按下<kbd>Esc</kbd>输入`:wq`保存退出

先运行`fcitx5`，再打开`fcitx5-configtool`配置输入法（后面按下<kbd>Ctrl</kbd>+<kbd>空格</kbd>来切换输入法），还可以设置一下皮肤、是否启用云拼音等

```bash
feh --randomize --bg-fill ~/Downloads/demo.jpg
```

编辑.xinitrc：

```bash
vim ~/.xinitrc
```

在文档末`fi`下面插入（注意还有一个`&`符号！）：

```bash
......
	unset f
fi # 下面插入！如果下面有东西就删掉！

exec feh --randomize --bg-fill ~/Downloads/demo.jpg &
exec picom -CGb &
exec fcitx5 &
exec slstatus &
exec dwm
```

接着重新启动dwm即可

## 5.安装显卡驱动

### Intel核心显卡

```bash
sudo pacman -S xf86-video-intel lib32-mesa vulkan-intel lib32-vulkan-intel
```

**Intel显卡驱动导致部分录屏软件花屏的解决办法**：

```bash
sudo vim /etc/X11/xorg.conf.d/00-modesetting.conf
```

添加以下内容：

```bash
Section "Device"
   Identifier "Intel"
   Driver "modesetting"
EndSection
```

注销或重启即可生效

### NVIDIA独立显卡

安装驱动：

```bash
sudo pacman -S nvidia
```

完成后重启系统（因为要屏蔽nouveau内核模块），接着生成Xorg服务器配置文件：

```bash
nvidia-xconfig
```

完成后重启系统即可

**NVIDIA显卡驱动导致部分录屏软件花屏的解决办法**：

打开“NVIDIA X 服务器设置”软件，在`OpenGL Settings`下将“Allow Flipping”选项关掉，完成后关闭软件即可

重启系统后会失效，如果你想永久设置（不推荐，会导致游戏轻微掉帧），那么请：

1. 修改配置文件

   ```bash
   sudo vim /etc/X11/xorg.conf
   ```

2. 如果配置文件中存在DRI，请把它注释掉

   ```bash
   #    Load        "dri"
   ```

3. 修改`Section "Device"`选项，修改`Option "NoFlip" "false"`为`Option "NoFlip" "true"`

   ```bash
   Section "Device"
      Identifier     "Device0"
      Driver         "nvidia"
      VendorName     "NVIDIA Corporation"
      Option "NoFlip" "true"
   EndSection
   ```

4. 重启系统后生效

## 6.安装系统监视软件

安装conky：

```bash
sudo pacman -S conky
```

创建conky配置文件：

```bash
vim ~/.conkyrc
```

并在里面输入：

```bash
background no
use_xft yes
xftfont Microsoft Yahei:size=14
xftalpha 0.75
update_interval 5.0
total_run_times 0
own_window yes
own_window_type override
own_window_transparent yes
own_window_hints undecorated,below,skip_taskbar,sticky,skip_pager
double_buffer yes
default_color grey
alignment middle_right
no_buffers yes
uppercase no
cpu_avg_samples 2
net_avg_samples 2
override_utf8_locale no

TEXT
${color #f4606c}${alignc}${nodename} ${uptime_short}

${color #f4606c}CPU: ${color grey}$cpu%
${color #f4606c} ${cpugraph 16,140 000000 7f8ed3}
${color #f4606c}RAM: $color$mem/$memmax
${color #f4606c} ${membar 6,140}

${color #f4606c}File Systems:
${color #f4606c}/ $color${fs_free /}
${color #f4606c} ${fs_bar 6,140 /}

${color grey}In: ${tcp_portmon 1 32767 count}  Out: ${tcp_portmon 32768 61000 count}${alignr}
```

保存退出后，编辑`~/.xinitrc`文件，在`exec dwm`前面插入`exec conky &`，如：

```bash
exec feh --randomize --bg-fill ~/Pictures/Wallpapers/sky.png &
exec picom -CGb &
exec fcitx5 &
exec slstatus &
exec conky &
exec dwm
```

## 7.安装常用软件

### PCManFM文件管理器

```bash
sudo pacman -S pcmanfm
```

### 火焰截图

```bash
sudo pacman -S flameshot
```

### Typora笔记

```bash
yay -S typora-free # 免费版本
yay -S typora # 收费版本
```

### XMind思维导图

```bash
yay -S xmind
```

### BleachBit系统清理

```bash
sudo pacman -S bleachbit
```

### mpv影音播放

```bash
sudo pacman -S mpv
```

### SimpleScreenRecorder录屏

```bash
sudo pacman -S simplescreenrecorder
```

### WPS Office办公

```bash
yay -S wps-office-cn ttf-wps-fonts wps-office-mui-zh-cn
```

### Scrcpy设备投屏

```bash
sudo pacman -S scrcpy
```

### SublimeText文本编辑

```bash
yay -S sublime-text-4
```

### GIMP图像编辑

```bash
sudo pacman -S gimp
```