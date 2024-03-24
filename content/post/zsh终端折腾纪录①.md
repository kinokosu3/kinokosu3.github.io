---
title: zsh终端折腾纪录①
date: 2017-08-19 19:16:41
tags:
    - zsh
categories: 备忘录
---
***

### 安装zsh

* 如果你是mac用户，系统已经自带有了，可以通过命令`cat /etc/shells`查看

* 其他的liunx系统就从软件源安装就好了，Ubuntu，debian`apt-get install zsh`, centos,redhat`yum apt-get zsh`

### 安装oh-my-zsh

虽然zsh功能强大，但是配置复杂，如果你专研过算法导论编译原理可以自己写一套。。。。所以国外有大神写了一套配置oh-my-zsh

`git clone`安装方法:

#### 克隆仓库

```shell
$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

#### 创建一个新的zsh配置文件

```shell
$ cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

#### 改变默认的shell

```shell 
$ chsh -s /bin/zsh
```
重新打开终端就好了

<!--more-->

### pip安装powerline-status

```python
pip install powerline-status
```

### 使用的powerline-themes

github链接[oh-my-zsh-powerline-theme](https://github.com/jeremyFreeAgent/oh-my-zsh-powerline-theme)

将文件中的`powerline.zsh-theme`复制到路径`~/.oh-my-zsh/themes`

然后更改文件`vim .zshrc`,更改：

```shell
ZSH_THEME="powerline"
```

添加：

```shell
export TERM="xterm-256color"
```

### powerline字体

github链接[链接](https://github.com/powerline/powerline)

然后安装powerline字体


### 使用iterm2导入字体

preferences->profiles->colors->color presets 导入主题颜色

text有字体选择，change font，选择powerline字体

### vim powerline开启

打开`vim .vimrc`

```shell
set rtp+=/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/powerline/bindings/vim/
set laststatus=2
set t_Co=256
```
`rtp+=`后面跟的路径是在python的库文件夹`site-packages`里面，我们可以在交互模式下输入`import site; site.getsitepackages()`得到路径



### zsh插件开启

修改`.zshrc`

```shell
pulgins(<插件1>, <插件2>)
```

### 自动补全插件incr安装

官网[链接](http://mimosa-pudica.net/zsh-incremental.html)

在`~/.oh-my-ssh/plugins/`新建一个目录incr(注意要使用sudo创建)，然后新建一个文件`sudo vim incr-0.2.zsh`,把插件内容全部复制进去，然后配置`.zshrc`文件，在文档最后面插入

```shell
source ~/.oh-my-zsh/plugins/incr/incr*.zsh
```

防止某天插件链接挂了，下面放出插件内容
```shell
# Incremental completion for zsh
# by y.fujii <y-fujii at mimosa-pudica.net>, public domain


autoload -U compinit
zle -N self-insert self-insert-incr
zle -N vi-cmd-mode-incr
zle -N vi-backward-delete-char-incr
zle -N backward-delete-char-incr
zle -N expand-or-complete-prefix-incr
compinit

bindkey -M viins '^[' vi-cmd-mode-incr
bindkey -M viins '^h' vi-backward-delete-char-incr
bindkey -M viins '^?' vi-backward-delete-char-incr
bindkey -M viins '^i' expand-or-complete-prefix-incr
bindkey -M emacs '^h' backward-delete-char-incr
bindkey -M emacs '^?' backward-delete-char-incr
bindkey -M emacs '^i' expand-or-complete-prefix-incr

unsetopt automenu
compdef -d scp
compdef -d tar
compdef -d make
compdef -d java
compdef -d svn
compdef -d cvs

# TODO:
#     cp dir/

now_predict=0

function limit-completion
{
	if ((compstate[nmatches] <= 1)); then
		zle -M ""
	elif ((compstate[list_lines] > 6)); then
		compstate[list]=""
		zle -M "too many matches."
	fi
}

function correct-prediction
{
	if ((now_predict == 1)); then
		if [[ "$BUFFER" != "$buffer_prd" ]] || ((CURSOR != cursor_org)); then
			now_predict=0
		fi
	fi
}

function remove-prediction
{
	if ((now_predict == 1)); then
		BUFFER="$buffer_org"
		now_predict=0
	fi
}

function show-prediction
{
	# assert(now_predict == 0)
	if
		((PENDING == 0)) &&
		((CURSOR > 1)) &&
		[[ "$PREBUFFER" == "" ]] &&
		[[ "$BUFFER[CURSOR]" != " " ]]
	then
		cursor_org="$CURSOR"
		buffer_org="$BUFFER"
		comppostfuncs=(limit-completion)
		zle complete-word
		cursor_prd="$CURSOR"
		buffer_prd="$BUFFER"
		if [[ "$buffer_org[1,cursor_org]" == "$buffer_prd[1,cursor_org]" ]]; then
			CURSOR="$cursor_org"
			if [[ "$buffer_org" != "$buffer_prd" ]] || ((cursor_org != cursor_prd)); then
				now_predict=1
			fi
		else
			BUFFER="$buffer_org"
			CURSOR="$cursor_org"
		fi
		echo -n "\e[32m"
	else
		zle -M ""
	fi
}

function preexec
{
	echo -n "\e[39m"
}

function vi-cmd-mode-incr
{
	correct-prediction
	remove-prediction
	zle vi-cmd-mode
}

function self-insert-incr
{
	correct-prediction
	remove-prediction
	if zle .self-insert; then
		show-prediction
	fi
}

function vi-backward-delete-char-incr
{
	correct-prediction
	remove-prediction
	if zle vi-backward-delete-char; then
		show-prediction
	fi
}

function backward-delete-char-incr
{
	correct-prediction
	remove-prediction
	if zle backward-delete-char; then
		show-prediction
	fi
}

function expand-or-complete-prefix-incr
{
	correct-prediction
	if ((now_predict == 1)); then
		CURSOR="$cursor_prd"
		now_predict=0
		comppostfuncs=(limit-completion)
		zle list-choices
	else
		remove-prediction
		zle expand-or-complete-prefix
	fi
}

```


### 更多的选择（翻译）（oh-my-zsh-powerline-theme）

所有的选项都必须在你的`.zshrc`配置文件中修改

默认下，在powerline右侧显示日期和时间，如果你不想要时间，可以选择你想要的

```shell
POWERLINE_RIGHT_B="time replacement"
```

或者你不想显示任何内容：

```shell
POWERLINE_RIGHT_B="none"
```

如果你要在时间旁边显示日期：

```shell
POWERLINE_RIGHT_A="date"
```

如果你想在时间之前显示最后一个命令的退出代码

```shell
POWERLINE_RIGHT_A="exit-status"
```

或者你想要它（退出代码）只出现在错误运行中：

```shell
POWERLINE_RIGHT_A="exit-status-on-fail"
```

如果你要在时间之前显示上一个命令的日期或者非零退出代码

```shell
POWERLINE_RIGHT_A="mixed"
```

如果你要在时间之后显示自定义文本

```shell
POWERLINE_RIGHT_A="blackfire.io"
```

如果你想改变POWERLINE_RIGHT_A元素的颜色:

```shell
POWERLINE_RIGHT_A_COLOR_FRONT="black"
POWERLINE_RIGHT_A_COLOR_BACK="red"
```

不翻了其实都看得懂。。。













