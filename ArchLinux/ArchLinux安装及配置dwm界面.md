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
```

安装密钥

```bash
pacman -S archlinuxcn-keyring
```

如果在安装密钥的过程中报错`could not be locally signed`，可以先执行下面的命令：

```bash
pacman -Syu haveged
systemctl start haveged
systemctl enable haveged

rm -rf /etc/pacman.d/gnupg
pacman-key --init
pacman-key --populate archlinux
```

再安装archlinuxcn密钥：

```bash
pacman -S archlinuxcn-keyring
```

## 2.安装X窗系统、字体

X窗系统及其他：

```bash
pacman -S xorg-server xorg-xinit
pacman -S base-devel feh picom libxft
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
echo -e "DO NOT USE \"sudo rm -rf /*\" ON THIS PC."
alias l='lsblk'
alias n='neofetch'
alias e='exit'
alias u='sudo umount -R /mnt'
alias P='sudo poweroff'
alias R='sudo reboot'
alias S='sudo systemctl hybrid-sleep'
alias U='sudo pacman -Syyu'
alias N='export https_proxy=http://127.0.0.1:7890;export http_proxy=http://127.0.0.1:7890;export all_proxy=socks5://127.0.0.1:7890'
alias O='unset http_proxy;unset https_proxy'
alias C="google-chrome-stable --proxy-server=\"http://127.0.0.1:7890\""
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

设置开机自动启用数字键：

```bash
sudo pacman -S numlockx
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

numlockx &
exec feh --randomize --bg-fill ~/Downloads/demo.jpg &
exec picom -CGb &
exec fcitx5 &
exec slstatus &
exec dwm
```

注意，`numlockx &`必须添加在`exec`命令之前

接着重新启动dwm即可

## 5.安装显卡驱动

如果你不知道显卡情况，可运行以下命令获知：

```bash
lspci -k | grep -A 2 -E "(VGA|3D)"
```

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

构建NVIDIA内核需要`linux-headers`，请先安装：

```bash
sudo pacman -S linux-headers
```

安装驱动：

```bash
sudo pacman -S nvidia # GeForce 920以上
yay -S nvidia-470xx-dkms lib32-nvidia-utils # GeForce 630-920
yay -S nvidia-390xx-dkms lib32-nvidia-utils # GeForce 400/500/600
yay -S nvidia-340xx-dkms lib32-nvidia-utils # GeForce 8/9
```

完成后重启系统（因为要屏蔽nouveau内核模块），接着手动创建配置文件：

```bash
sudo vim /etc/X11/xorg.conf.d/20-nvidia.conf
```

```bash
Section "Device"
   Identifier     "Device0"
   Driver         "nvidia"
   VendorName     "NVIDIA Corporation"
EndSection
```

完成后重启系统即可

**NVIDIA显卡驱动导致部分录屏软件花屏的解决办法**：

1. 修改配置文件

   ```bash
   sudo vim /etc/X11/xorg.conf.d/20-nvidia.conf
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

### AMD独立显卡

安装驱动：

```bash
sudo pacman -S lib32-mesa xf86-video-amdgpu vulkan-radeon amdvlk # HD 6000以上
sudo pacman -S lib32-mesa xf86-video-ati # HD 6000以下
```

* HD 6000以上

  使用Vim编辑`/etc/X11/xorg.conf.d/20-amdgpu.conf`文件

  ```bash
  Section "Device"
       Identifier "AMD"
       Driver "amdgpu"
  EndSection
  ```

* HD 6000以下

  使用Vim编辑`/etc/X11/xorg.conf.d/20-radeon.conf`文件

  ```bash
  Section "Device"
      Identifier "Radeon"
      Driver "radeon"
  EndSection
  ```

注销或重启即可生效

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
sudo pacman -S gvfs pcmanfm
```

### Clash科学上网

```bash
yay -S clash-for-windows-bin
```

**配置代理**：

1. 打开Clash，将`Port`（端口号）设置为`7890`
2. 在`Profiles`导入机场（订阅地址），然后在`Proxies`设置源即可

**通过sudo保持代理**：

```bash
sudo vim /etc/sudoers.d/05_proxy
```

```bash
Defaults env_keep += "*_proxy *_PROXY"
```

### Minecraft我的世界

存储Minecraft启动器账户登录信息需要先安装`gnome-keyring`

```bash
sudo pacman -S gnome-keyring
```

使用Vim编辑`~/.xinitrc`，添加：

```bash
eval $(/usr/bin/gnome-keyring-daemon --start --components=pkcs11,secrets,ssh)
export SSH_AUTH_SOCK
```

最后安装Minecraft：

```bash
yay -S minecraft-launcher
```

安装Forge，请前往[https://files.minecraftforge.net](https://files.minecraftforge.net)下载文件，然后使用`java -jar <file>`启动下载的文件

安装Optifine，请前往[https://optifine.net](https://optifine.net)下载文件，然后放到`.minecraft`下的`mods`文件夹

---

PVP玩家可以选择：

Lunar客户端：

```bash
yay -S lunar-client
```

Badlion客户端：

```bash
yay -S badlion-client
```

---

解决Minecraft旧版本无法输入中文的问题：

**1.安装软件包**

```bash
sudo pacman -S zenity xbindkeys xdotool
```

**2.设置脚本**

```bash
vim ~/.mcinput.sh
```

下面的脚本来自依云's Blog，链接：[https://blog.lilydjwg.me/2015/5/17/input-chinese-to-minecraft-in-linux.93167.html](https://blog.lilydjwg.me/2015/5/17/input-chinese-to-minecraft-in-linux.93167.html)，非常感谢他解决了我的问题

```bash
chars=$(zenity --title 中文输入 --text 中文输入 --width 500 --entry 2>/dev/null)
sleep 0.1
xdotool key --delay 150 Escape t
sleep 0.2
xdotool type --delay 150 "$chars"
xdotool key Return
```

**3.设置全局热键F12**

生成默认配置文件

```bash
xbindkeys --defaults > ~/.xbindkeysrc
```

辅助按键显示，在空白窗口弹出后按下F12

```bash
xbindkeys -k
```

接着把终端输出的内容复制到`~/.xbindkeysrc`，再编辑一下该按键要执行的命令，如：

```bash
vim ~/.xbindkeysrc
```

```bash
"sh ~/.mcinput.sh"
    m:0x0 + c:96
    F12
```

最后启动Xbindkeys

```bash
xbindkeys
```

设置进入桌面自动启动Xbindkeys

```bash
vim ~/.xinitrc
```

```bash
# 添加到exec命令之前
xbindkeys &
exec your_window_manager
```

完成了，以后在打开MC时按下F12输入你想输入的内容，然后按下回车就可以直接输入中文了

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

### XP-Pen数位板驱动

```bash
yay -S xp-pen-tablet
```

需要以root账户运行的解决办法：

```bash
sudo chown $(whoami) /usr/lib/pentablet
sudo chmod +x /usr/lib/pentablet/pentablet
```

Chrome手写输入插件：**Google 输入工具**

如果你有电子白板的需求，可以安装OpenBoard

```bash
yay -S openboard
```

### Android Studio安卓开发

```bash
yay -S android-studio
```

修复启动时空白界面的问题：

```bash
sudo vim /etc/environment
```

```bash
_JAVA_AWT_WM_NONREPARENTING=1
```

Git图形界面管理器：

```bash
yay -S gitkraken
```

## 8.常规设置

### 调节鼠标灵敏度

安装`xorg-xinput`：

```bash
sudo pacman -S xorg-xinput
```

查看鼠标ID：

```bash
xinput list
```

设置鼠标灵敏度：

```bash
xinput --set-prop 8 'libinput Accel Speed' -0.6
```

注意：“-0.6”是要设置的鼠标灵敏度（范围为[-1,1]），“8”要修改为你自己鼠标的ID！
