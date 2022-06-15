# ArchLinux安装与配置KDE桌面

## 1.更新系统

```bash
pacman -Syyu
```

## 2.创建新用户

```bash
useradd -m -g users -G wheel -s /bin/bash yuxiang
```

设置密码：

```bash
passwd yuxiang
```

## 3.编辑新用户权限

```bash
EDITOR=vim visudo
```

按下<kbd>/</kbd>搜索`wheel`，然后<kbd>Enter</kbd>

定位到`# %wheel ALL=(ALL) ALL`，在`#`号下按**2**次<kbd>X</kbd>删除注释

按<kbd>Esc</kbd>输入`:wq`退出Vim

## 4.安装KDE桌面环境

```bash
pacman -S plasma-meta konsole dolphin bash-completion
```

按**3**次<kbd>Enter</kbd>安装

将SDDM设置为开机自启

```bash
systemctl enable sddm
```

开启32位支持库以及ArchLinuxCN支持库

```bash
vim /etc/pacman.conf
```

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

好了，输入`reboot`重启！

## 5.进入桌面的配置

进入桌面后，打开`Konsole`终端进行操作

安装必要插件：

* 安装ntfs-3g：

  ```bash
  sudo pacman -S ntfs-3g
  ```

* 安装开源字体

  ```bash
  sudo pacman -S adobe-source-han-serif-cn-fonts wqy-zenhei
  ```

  ```bash
  sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
  ```

* 安装基本环境

  ```bash
  sudo pacman -S git base-devel
  ```

* 安装Chrome

  ```bash
  sudo pacman -S archlinuxcn-keyring
  ```

  ```bash
  sudo pacman -S yay
  ```
  
  ```bash
  yay -S google-chrome
  ```


将系统设置为中文

在启动菜单上找到`System Settings`并启动，点击`Regional Settings`，在`Language`一栏点击`Add languages...`按钮，拉到下面找到`简体中文`，点击后点`Add`，把它拉到最上面，点击`Apply`按钮，完成

再将Windows下的字体文件`msyh.ttf`、`msyhbd.ttf`、`simhei.ttf`复制到`/usr/share/fonts/`目录下

注销重新登录即可看到效果

## 6.配置中文输入法

安装fcitx输入法

```bash
sudo pacman -S fcitx5-im
```

安装fcitx提供的中文输入包

```bash
sudo pacman -S fcitx5-chinese-addons
```

安装主题皮肤

```bash
sudo pacman -S fcitx5-material-color
```

配置：进入`系统设置`，单击`区域设置`，单击`输入法`，单击`运行 Fcitx`，点击`添加输入法...`，在列表框中找到`Pinyin`并点击`添加`，再点一下`pinyin`旁边的`配置`按钮，勾选`在程序中显示预编辑文本`、勾选`启用云拼音`，返回输入法配置页面

单击`配置附加组件...`，在“界面”列表中找到`Classic User Interface`，点击它旁边的设置图标按钮，在主题下拉列表中选择`Material-Color-Blue`（根据个人喜好）

设置环境变量，让其它程序也能使用这个中文输入法

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

接下来设置输入法开机启动：

点开启动菜单的`系统设置`，点击`开机和关机`，找到`自动启动`，点击`添加程序`，输入`fcitx`，单击`fcitx 5`后单击确定退出，完成，`Log out`重新登录即可看到效果

好了，按下<kbd>Ctrl</kbd>+<kbd>空格</kbd>来切换输入法

## 7.安装显卡驱动

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
