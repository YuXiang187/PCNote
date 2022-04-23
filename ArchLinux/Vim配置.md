# Vim配置

## 0.配置文件（无插件版）

```bash
syntax on
set relativenumber
set cursorline
set wrap
set showcmd
set wildmenu
set hlsearch
set incsearch
set ignorecase
set nocompatible
set fileencodings=utf-8,gbk,utf-16le,cp1252,iso-8859-15,ucs-bom
set termencoding=utf-8
set encoding=utf-8
set scrolloff=4
set clipboard=unnamedplus

let mapleader = " "

vnoremap <LEADER>p "+p
vnoremap <LEADER>y "+y

map Q :q<CR>
map W :w<CR>
map ro :set relativenumber<CR>
map rf :set norelativenumber<CR>

map sl :set splitright<CR>:vsp<CR>
map sh :set nosplitright<CR>:vsp<CR>
map sk :set nosplitbelow<CR>:sp<CR>
map sj :set splitbelow<CR>:sp<CR>

map wl <C-w>l
map wk <C-w>k
map wh <C-w>h
map wj <C-w>j

map tn :tabe<CR>
map tl :tabn<CR>
map th :tabp<CR>

map <LEADER>fd /\(\<\w\+\>\)\_s*\1
map <LEADER>sc :set spell!<CR>
map <LEADER><LEADER> <Esc>/<++><CR>:nohlsearch<CR>c4l
map <LEADER>se :g/^\s*$/d<CR>:nohlsearch<CR>
map <LEADER>a ggvG$
map <LEADER>ns :nohlsearch<CR>
map <LEADER>d Vyp
map <LEADER>da <Esc>:r !echo `date "+\%Y-\%m-\%d \%H:\%M:\%S"`<CR>

inoremap jj <Esc>
```

## 1.配置文件（插件版）

```bash
syntax on
set relativenumber
set cursorline
set wrap
set showcmd
set wildmenu
set hlsearch
set incsearch
set ignorecase
set nocompatible
set fileencodings=utf-8,gbk,utf-16le,cp1252,iso-8859-15,ucs-bom
set termencoding=utf-8
set encoding=utf-8
set scrolloff=4
set clipboard=unnamedplus

let mapleader = " "
let &t_SI = "\<Esc>]50;CursorShape=1\x7"
let &t_SR = "\<Esc>]50;CursorShape=2\x7"
let &t_EI = "\<Esc>]50;CursorShape=0\x7"

vnoremap <LEADER>p "+p
vnoremap <LEADER>y "+y

map Q :q<CR>
map W :w<CR>
map ro :set relativenumber<CR>
map rf :set norelativenumber<CR>

map sl :set splitright<CR>:vsp<CR>
map sh :set nosplitright<CR>:vsp<CR>
map sk :set nosplitbelow<CR>:sp<CR>
map sj :set splitbelow<CR>:sp<CR>

map wl <C-w>l
map wk <C-w>k
map wh <C-w>h
map wj <C-w>j

map tn :tabe<CR>
map tl :tabn<CR>
map th :tabp<CR>

map <up> :res-1<CR>
map <down> :res+1<CR>
map <left> :vertical resize-1<CR>
map <right> :vertical resize+1<CR>

map <LEADER>fd /\(\<\w\+\>\)\_s*\1
map <LEADER>sc :set spell!<CR>
map <LEADER><LEADER> <Esc>/<++><CR>:nohlsearch<CR>c4l
map <LEADER>se :g/^\s*$/d<CR>:nohlsearch<CR>
map <LEADER>a ggvG$
map <LEADER>ns :nohlsearch<CR>
map <LEADER>d Vyp
map <LEADER>da <Esc>:r !echo `date "+\%Y-\%m-\%d \%H:\%M:\%S"`<CR>

inoremap jj <Esc>

call plug#begin('~/.vim/plugged')
Plug 'vim-airline/vim-airline'
Plug 'connorholyday/vim-snazzy'
Plug 'preservim/nerdtree'
Plug 'mg979/vim-visual-multi'
Plug 'tpope/vim-speeddating'
call plug#end()

map tt :NERDTreeToggle<CR>
let NERDTreeMapOpenExpl = ""
let NERDTreeMapUpdir = ""
let NERDTreeMapUpdirKeepOpen = "l"
let NERDTreeMapOpenSplit = ""
let NERDTreeOpenVSplit = ""
let NERDTreeMapActivateNode = "i"
let NERDTreeMapOpenInTab = "o"
let NERDTreeMapPreview = ""
let NERDTreeMapCloseDir = "n"
let NERDTreeMapChangeRoot = "y"

let g:SnazzyTransparent = 1

color snazzy
```

## 2.安装插件

> 推荐

* `vim-airline/vim-airline`
* `connorholyday/vim-snazzy`
* `preservim/nerdtree`
* `vim-visual-multi`
* `tpope/vim-speeddating`

> 安装示例

1. 前往[https://github.com/junegunn/vim-plug](https://github.com/junegunn/vim-plug)下载`plug.vim`插件

2. 安装插件

   ```bash
   mkdir -p  ~/.vim/autoload/
   cp plug.vim  ~/.vim/autoload
   ```

3. 添加或删除插件

   编辑 ~/.vimrc 文件中的内容

   ```bash
   call plug#begin('~/.vim/plugged')
   Plug 'vim-airline/vim-airline'
   Plug 'connorholyday/vim-snazzy'
   Plug 'preservim/nerdtree'
   Plug 'mg979/vim-visual-multi'
   Plug 'tpope/vim-speeddating'
   call plug#end()
   ```

   运行命令重新加载：

   ```bash
   :source ~/.vimrc
   ```

4. 安装或卸载插件（需要先安装`git`）

   打开 vim 使用命令：`PlugInstall`
   打开 vim 使用命令：`PlugClean`