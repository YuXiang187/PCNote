# ArchLinux下Wine的使用

安装必要的包：

```bash
sudo pacman -S wine wine-mono winetricks zenity
```

安装完后输入`vim ~/.bashrc`，往里面插入：

```bash
export WINEARCH="win32"
```

保存并退出，重启系统

进入桌面后运行：

```bash
winecfg
```

把操作系统改为`Windows 10`

复制Windows下的字体文件到`~/.wine/drive_c/windows/Fonts`，然后运行：

```bash
winetricks
```

选择`Select the default wineprefix`（选择默认的wine容器），然后再选择`Run uninstaller`，单击`Install...`选择安装包安装程序

最后在终端输入`wine <exe>`运行你喜欢的程序吧！

## 使用Wine运行Photoshop CS6

> 注意：**一定要使用绿色版的Photoshop！**

1. 在终端输入`winecfg`，然后点击“**libraries**”选项卡，单击“New overrides for library:”下面的文本选择框，然后添加`msvcp100`库

2. 选中`msvcp100`后单击“**Edit...**”，选择“**Native then Builtin**”（也就是原装先于内建）

3. 使用`wine`命令运行Photoshop的绿化程序

   ```bash
   wine <exe>
   ```

4. 使用`winetricks`命令安装atmlib

   ```bash
   winetricks atmlib
   ```

   警告：文件[http://x3270.bgp.nu/download/specials/W2KSP4_EN.EXE](http://x3270.bgp.nu/download/specials/W2KSP4_EN.EXE)下载可能非常慢，可以使用第三方下载器下载，然后将文件放到`~/.cache/winetricks/win2ksp4`文件夹下

5. 创建启动器入口（可选）

   可以在`home`目录新建一个`.start.sh`的隐藏文件，然后执行`chmod +x .start.sh`命令加权

   然后在`/.local/share/applications/`目录下创建一个`start.desktop`文件，输入以下内容：

   ```bash
   [Desktop Entry]
   Version=1.0
   Name=Photoshop
   Comment=A photo editer.
   Exec='/home/yuxiang/.start.sh'
   Terminal=false
   Type=Application
   Icon=/home/yuxiang/.logo/start.png
   ```

   注意，`Exec`和`Icon`的路径要改成你自己的
   
6. 创建命令行入口（可选）

   在终端内输入`vim ~/.bashrc`打开Bash的配置文件，然后添加以下内容：

   ```bash
   alias photoshop='wine ~/.wine/drive_c/Program\ Files/Adobe\ Photoshop\ CS6/Adobe\ Photoshop\ CS6/Photoshop.exe'
   ```

   保存退出后在终端输入`source ~/.bashrc`刷新缓存，以后就可以使用`photoshop`命令进入PS了

如果使用PS打开图片显示的是黑屏，那么请取消勾选“首选项(N)”下的“性能”下的“使用图形处理器”