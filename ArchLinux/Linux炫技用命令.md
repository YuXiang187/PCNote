# Linux炫技用命令

## 1.终端小火车

```bash
sudo pacman -S sl
```

使用：`sl <option>`

* `-a`：火车上的人们会呼救
* `-l`：显示小一点的火车
* `-F`：会飞的火车
* `-e`：允许按下<kbd>Ctrl</kbd>+<kbd>C</kbd>中断火车

无限小火车：`while true;do sl;done`

## 2.终端代码雨

```bash
sudo pacman -S cmatrix
```

使用：`cmatrix <option>`

- `-b`：随机粗体

- `-B`：全部粗体

- `-o`：使用旧风格滚动（不好看）

- `-x`：显示不一样的符号

- `-u`：滚动的快慢，0-9任意选

- `-C`：显示的颜色，默认`green`

  可选：`red`、`blue`、`white`、`yellow`、`cyan`、`magenta`、`black`

按下<kbd>Ctrl</kbd>+<kbd>C</kbd>或者<kbd>Q</kbd>退出

## 3.终端自动炫技

```bash
yay -S hollywood
```

使用：`hollywood`

## 4.终端会说话的牛

```bash
sudo pacman -S cowsay
```

使用：`cowsay "Hello"`（终端输出`Hello`）

* `-l`：查动物
* `-f <name>`：换动物

## 5.终端打效果字

```bash
yay -S toilet
```

例如：

```bash
toilet -f mono12 -F metal OpenSkill
```

* `-f <name>`：设置字体
* `-w <width>`：设置输出宽度
* `-t`：适应终端宽度
* `-F <option>`：
  * `crop`：纯白色
  * `rainbow`：彩虹
  * `metal:`：金属
  * `flip`：镜像水平反转
  * `flop`：镜像垂直反转
  * `180`：旋转180度
  * `left`：左垂直显示
  * `right`：右垂直显示
  * `border`：加边框
* `-E html`：导出为HTML

## 6.终端打大字

```bash
sudo pacman -S figlet
```

使用`figlet <text>`即可输出大字