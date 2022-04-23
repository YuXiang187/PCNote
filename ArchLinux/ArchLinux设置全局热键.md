# ArchLinux设置全局热键

## 1.模拟按键

安装模拟按键软件xdotool

```bash
sudo pacman -S xdotool
```

模拟两个按键<kbd>Alt</kbd>+<kbd>Tab</kbd>

```bash
xdotool key alt+Tab
```

自动输入`word`

```bash
xdotool type 'word'
```

模拟鼠标点击：移动到`(x,y)`，然后点击鼠标左键（1左，2中，3右）

```bash
xdotool mousemove 655 320 click 1
```

重复点击鼠标右键1次

```bash
xdotool click -repeat 1 3
```

获取鼠标位置

```bash
xdotool getmouselocation
```

## 2.全局热键

安装全局热键设置软件xbindkeys

```bash
sudo pacman -S xbindkeys
```

生成默认配置文件

```bash
xbindkeys --defaults > ~/.xbindkeysrc
```

辅助按键显示

```bash
xbindkeys -k
```

重启xbindkeys

```bash
killall xbindkeys
xbindkeys
```

## 3.示例配置

认识/下一个（/）

```bash
xdotool mousemove 91 712 click 1
```

模糊（*）

```bash
xdotool mousemove 205 712 click 1
```

不认识/记错了（-）

```bash
xdotool mousemove 316 712 click 1
```

拼写（+）

```bash
xdotool mousemove 306 63 click 1
```

隐藏输入法（`）

```bash
xdotool mousemove 354 520 click 1
```

选项一（1）

```bash
xdotool mousemove 199 329 click 1
```

选项二（2）

```bash
xdotool mousemove 199 410 click 1
```

选项三（3）

```bash
xdotool mousemove 199 487 click 1
```

选项四（4）

```bash
xdotool mousemove 199 569 click 1
```

开始拼写（9）

```bash
xdotool mousemove 278 555 click 1
```

继续复习（0）

```bash
xdotool mousemove 287 700 click 1
```