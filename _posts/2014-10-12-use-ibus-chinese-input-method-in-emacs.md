---
title: "如何在emacs中是使用ibus中文输入法?"
layout: post
photo_url: /res/emacs.png
comments: yes
---
## 如何在emacs中是使用ibus中文输入法?

在emacs中是使用ibus中文输入法,需要自己手动配置, 具体如下:

1. 安装 ibus.el: 在命令行下输入 sudo apt-get install ibus-el. 若提示需要安装依赖文件,请一同安装
2. 配置 .emacs文件(在用户目录下), 配置语句如下:
    <pre><code>
    (require 'ibus)
    (add-hook 'after-init-hook 'ibus-mode-on)  ;; 自动开启ibus-mode模式
    (ibus-define-common-key ?\C-\s nil)  ;; 取消Ctrl+Space的切换输入法,可能需要手动将ibus的切换快捷键改为Shift+Space
    (ibus-define-common-key ?\C-/ nil)  ;; C-/:撤消快捷键
    (setq ibus-cursor-color '("red" "blue" "limegreen")) ;; Change cursor color depending on IBus status
    </code></pre>
> Note: 上述代码全用英文字符, 复制容易出错.

一般情况下,按照上述两步操作基本搞定.

* 在emacs编辑状态下,中英文切换的快捷键为: Ctrl+\\.
* 字符换行快捷键为:Ctrl+n(向下); Ctrl+p(向上).

倘若你的linux为英文系统,可能没有中文的locale.还需要如下操作:

* 方法一,在终端命令行下启动emacs: LC_CTYPE=zh_CN.utf8 emacs
* 方法二,修改语言环境,在.bash_profile里 输入: export LANG=zh_CN.UTF-8(有问题)
使用以上两种方法,在启动emacs时会报 Gtk-WARNING **: Locale not supported by C library......
* 方法三,修改locale.(TODO)
