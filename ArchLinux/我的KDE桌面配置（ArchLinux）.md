# 我的KDE桌面配置（ArchLinux）

## 1.系统主题及面板配置

### 系统设置及主题配置

> 主题设置

* 安装全局主题：Breeze 微风深色
* 应用程序风格：Breeze 微风
* Plasma 视觉风格：Layan
* 窗口装饰元素：Layan-solid
* 颜色：Layan
* 图标：Tela blue Dark
* 光标：Breeze 微风
* 欢迎屏幕：Arch Splash
* SDDM：Layan

> 桌面特效

* 对话框显隐过度
* 窗口惯性晃动
* 窗口粉碎动画
* 最小化过渡动画：神灯
* 窗口打开/关闭动效：滑翔
* 虚拟桌面3D切换动效

> 虚拟桌面

* 主桌面
* 副桌面

> 锁屏

* 自动锁定屏幕：如果空闲`8`分钟
* 外观配置：Your Name

> 快捷键

* KWin：切换下一个桌面<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Tab</kbd>

> 显示和监控

* 混成器：选择平滑，选择`OpenGL2.0`
* 打开夜晚颜色

> 终端配置

* 透明度设置为40%

### 面板配置

> 顶部菜单栏面板从左到右如下布局

* 应用程序菜单：设置为Arch Linux图标
* Application title：无窗口时设置显示文本“Arch Linux”
* 全局菜单
* 面板间距
* 便利贴：设置半透明白字便利贴
* 系统托盘
* 数字时钟：仅显示时钟
* 锁定/注销：仅显示关机键

> 可选Plasma插件：Simple System Monitor、Smart Video Wallpaper

Smart Video Wallpaper应用后桌面会黑屏，解决办法如下：

```bash
sudo pacman -S mpv gst-plugins-base gst-plugins-ugly gst-libav gst-plugins-bad
```

Simple Monitor无法显示信息的解决办法如下：

```bash
sudo pacman -S ksysguard
```

以上设置重启系统后生效

## 2.安装软件及其配置

### Latte - 底部栏

> 安装

```bash
sudo pacman -S latte-dock
```

> 设置

大小为`56`像素

> 固定的应用

* Dolphin
* Google Chrome
* XMind
* 火焰截图
* BleachBit
* WPS 2019
* Typora
* VLC

### 火焰截图 - 截图

```bash
sudo pacman -S flameshot
```

### Okular - PDF阅读器

```bash
sudo pacman -S okular
```

### Typora - 笔记

```bash
yay -S typora-free # 免费版本
yay -S typora # 收费版本
```

### WPS Office - 办公

```bash
yay -S wps-office-cn ttf-wps-fonts wps-office-mui-zh-cn
```

### XMind - 思维导图

```bash
yay -S xmind
```

### nomacs - 图像查看

```bash
sudo pacman -S nomacs
```

### VLC - 影音播放

```bash
sudo pacman -S vlc
```

### SimpleScreenRecorder - 录屏

```bash
sudo pacman -S simplescreenrecorder
```

### BleachBit - 系统清理

```bash
sudo pacman -S bleachbit
```

### KDE Connect - 手机互联

```bash
sudo pacman -S kdeconnect
```

### Clash - 科学上网

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

### Sublime Text - 文本编辑

```bash
yay -S sublime-text-4
```

### Scrcpy - Android设备投屏

```bash
sudo pacman -S scrcpy
```
