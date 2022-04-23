# 自定义suckless软件

## 1.克隆源代码

```bash
git clone https://git.suckless.org/dwm
git clone https://git.suckless.org/st
git clone https://git.suckless.org/dmenu
git clone https://git.suckless.org/slstatus
```

## 2.安装补丁

dwm必装补丁（1个）：**systray**

st必装补丁（3个）：

* **alpha**（自行修改透明度：7.5）
* **anysize**
* **scrollback**

安装补丁：

```bash
patch -p1 < xx.diff
```

## 3.编辑源代码

### dwm

config.h

```bash
static const unsigned int borderpx  = 0;
static const int topbar             = 0;
static const char col_gray4[]       = "#f4606c";
static const char col_cyan[]        = "#222222";
```

```bash
static const char *fonts[]          = { "Microsoft Yahei:size=14:antialias=true:autohint=true","Symbols Nerd Font:pixelsize=22:antialias=true:autohint=true" };
static const char dmenufont[]       = "Microsoft Yahei:size=14:antialias=true:autohint=true";
```

```bash
static const Rule rules[] = {
	/* xprop(1):
	 *	WM_CLASS(STRING) = instance, class
	 *	WM_NAME(STRING) = title
	 */
	/* class      instance    title       tags mask     isfloating   monitor */
	{ "Pcmanfm",  NULL,       NULL,       0,            1,           -1 },
	{ "flameshot",NULL,       NULL,       0,            1,           -1 },
};
```

```bash
static const Layout layouts[] = {
	/* symbol     arrange function */
	{ "Tile",      tile },    /* first entry is default */
	{ "Null",      NULL },    /* no layout function means floating behavior */
	{ "[M]",      monocle },
};
```

```bash
#define MODKEY Mod4Mask
```

* 启动器的快捷键改为<kbd>Super</kbd>+<kbd>A</kbd>
* 关闭窗口的快捷键改为<kbd>Super</kbd>+<kbd>Shift</kbd>+<kbd>Q</kbd>
* 退出dwm的快捷键改为<kbd>Super</kbd>+<kbd>Shift</kbd>+<kbd>E</kbd>
* 打开终端的快捷键改为<kbd>Super</kbd>+<kbd>Enter</kbd>
* 提升窗口的快捷键改为<kbd>Super</kbd>+<kbd>Shift</kbd>+<kbd>Enter</kbd>

~/.upvol.sh

```bash
amixer set Master 5%+
```

~/.downvol.sh

```bash
amixer set Master 5%-
```

加权：

```bash
chmod +x ~/.upvol.sh
chmod +x ~/.downvol.sh
```

---

```bash
static const char *webcmd[]   = { "google-chrome-stable", NULL };
static const char *filecmd[]  = { "pcmanfm", NULL };
static const char *upvol[]    = { "/home/yuxiang/.upvol.sh", NULL };
static const char *downvol[]  = { "/home/yuxiang/.downvol.sh", NULL };
```

* 打开Chrome的快捷键改为<kbd>Super</kbd>+<kbd>S</kbd>
* 打开PCManFM的快捷键改为<kbd>Super</kbd>+<kbd>C</kbd>
* 调高音量快捷键改为<kbd>Super</kbd>+<kbd>Z</kbd>
* 调低音量快捷键改为<kbd>Super</kbd>+<kbd>Shift</kbd>+<kbd>Z</kbd>

### dmenu

config.h

```bash
static int topbar = 0;
```

```bash
static const char *fonts[] = {
	"Microsoft Yahei:size=14:antialias=true:autohint=true"
};
```

### slstatus

config.h

```bash
static const struct arg args[] = {
	/* function format          argument */
	{ run_command,	"\uf303 %s | ",	"uname -r | awk -F \"-\" ' { print $1 } '" },
	{ cpu_perc,	"\ufb19 %s%% | ",	NULL },
	{ ram_perc,	"\uf2db %s%% | ",	NULL },
	{ run_command,	"\uf028 %s | ",	"amixer sget Master | awk -F \"[][]\" ' /Mono:/ { print $2 }'" },
	{ datetime, "%s",           "%F %T" },
};
```

### st

config.mk

```bash
X11INC = /usr/include/X11
X11LIB = /usr/include/X11
```

config.h

```bash
static char *font = "monospace:pixelsize=20:antialias=true:autohint=true";
```

## 4.编译源代码

```bash
make
make clean
```

编译顺序：`dwm` - `dmenu` - `slstatus` - `st`