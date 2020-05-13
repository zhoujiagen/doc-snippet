# Emacs


> [GNU Emacs manual](https://www.gnu.org/software/emacs/manual/emacs.html)

```
brew install emacs
ln -Fs $(find /usr/local -name "Emacs.app") /Applications/Emacs.app
```

1 安装包

```
M-x install-package RET
<package-name> RET

M-x package-list-packages RET
```


2 安装和加载主题

主题库: [Colour schemes for a variety of editors created by Dayle Rees.](https://github.com/daylerees/colour-schemes)
展示: [Emacs Themes](https://emacsthemes.com/)

```
M-x install-package RET
slime-theme RET

M-x load-theme RET
slime RET
```

设置字体: [Set Fonts](https://www.emacswiki.org/emacs/SetFonts)

[stackoverflow: How to set the font size in Emacs?](; https://stackoverflow.com/questions/294664/how-to-set-the-font-size-in-emacs)

```
M-x customize-face RET default
```



3 快捷键

|分组|命令|说明|
|:---|:---|:----------------------------------|
|键绑定|C-h k `<key>` <br> M-x describe-key `<key>`|描述当前缓冲区的绑定到`<key>`的函数|
|键绑定|C-h b|列出当前缓冲区的所有键绑定|
|键绑定|C-h m|显示所有当前缓冲区可用的major-mode的命令，然后是所有的minor-mode的命令|
|键绑定|C-h l|按顺序显示出你刚刚按了的所有按键的序列|
|注释| ==M-x comment-region== |注释区域|
|注释| ==M-x uncomment-region== |取消注释区域|
|格式化| ==C-x h C-M-\\== |全选后格式化|


## auto-complete

> [Emacs auto-complete package](https://github.com/auto-complete/auto-complete)

```
M-x package-install [RET]
auto-complete [RET]
```

## ac-slime

> [Slime completion source for Emacs auto-complete package](https://github.com/purcell/ac-slime)

```
(require 'ac-slime
(add-hook 'slime-mode-hook 'set-up-slime-ac)
 (add-hook 'slime-repl-mode-hook 'set-up-slime-ac)
 (eval-after-load "auto-complete"
   '(add-to-list 'ac-modes 'slime-repl-mode))
```

## ParEdit

> [ParEdit](https://www.emacswiki.org/emacs/ParEdit)

ParEdit (paredit.el) is a minor mode for performing structured editing of S-expression data.

```
M-x package-install RET paredit RET
```

## Yasnippet

> [Yasnippet](https://www.emacswiki.org/emacs/Yasnippet)
>
> YASnippet is a template system for Emacs. It allows you to type an abbreviation and automatically expand it into function templates.

``` lisp
(require 'yasnippet)
(yas-global-mode 1)
```

- [YASnippet menu](https://joaotavora.github.io/yasnippet/snippet-menu.html)
- [Writing snippets](https://joaotavora.github.io/yasnippet/snippet-development.html)

## Slime

> [The Superior Lisp Interaction Mode for Emacs](https://common-lisp.net/project/slime/)

+ [Slime用户手册中文翻译版](https://github.com/unionx/slime-user-manual-cn)

安装:

```
M-x install-package RET
slime RET
```

配置: `.emacs`中添加
``` lisp
;; Set your lisp system and, optionally, some contribs
(setq inferior-lisp-program "/usr/local/bin/sbcl")
(setq slime-contribs '(slime-fancy))
```


使用:

```
M-x slime RET
; SLIME 2.22
CL-USER> (+ 1 1)
2
```

退出:

```
CL-USER> ,
# in minibuffer, Command:
sayoonara RET
# in minibuffer, Connection closed.
```


加载和编译文件:

``` lisp
# 在与lisp文件同一目录下
M-x slime RET

; SLIME 2.22
CL-USER> (load "hello.lisp")
T
CL-USER> (hello-world)
Hello, Common Lisp!!
NIL
CL-USER> (load (compile-file "hello.lisp"))
; compiling file "/.../hello.lisp" (written 07 AUG 2019 01:55:50 PM):
; compiling (DEFUN HELLO-WORLD ...)

; wrote /.../hello.fasl
; compilation finished in 0:00:00.002
T
CL-USER> (hello-world)
Hello, Common Lisp!!
NIL
CL-USER>
```

## 字符编码

[Emac Language Environment](https://www.emacswiki.org/emacs/LanguageEnvironment)

``` lisp
(set-terminal-coding-system "UTF-8")
```

REF: https://codeday.me/bug/20190303/737251.html


``` shell
# SBCL
LC_CTYPE=en_US.UTF-8
export LC_CTYPE
```

``` lisp
; SLIME 2.22
CL-USER> sb-impl::*default-external-format*
:US-ASCII
CL-USER> (setf sb-impl::*default-external-format* :UTF-8)
:UTF-8
CL-USER> sb-impl::*default-external-format*
:UTF-8
```
or in `~/.sbclrc`:

``` lisp
;;; set external format
(setf sb-impl::*default-external-format* :UTF-8)
```


## 快捷键

|分组|命令|说明|
|:-|:---------------------------------------|:--------------------------------|
|求值|==C-x C-e== <br> M-x slime-eval-last-expression|对光标前的表达式求值并且将结果显示到显示区|
|求值|==C-M-x== <br> M-x slime-eval-defun|对当前toplevel的形式进行求值并将结果打印到显示区|
|求值|C-c : <br> M-x slime-interactive-eval|从迷你缓冲区读取一个表达式并求值|
|求值|==C-c C-r== <br> M-x slime-eval-region|对区域进行求值|
|求值|==C-c C-p== <br> M-x slime-pprint-eval-last-expression|对光标前的表达式进行求值并将结果漂亮地打印在一个新的缓冲区里|
|求值|C-c E <br> M-x slime-edit-value|在一个叫做“Edit `<form>`”的新缓冲区里编辑一个可以setf的形式的值. 这个值会被插入一个临时缓冲区以便编辑，然后用C-c C-c命令来提交设置于Lisp中.|
|求值|C-x M-e <br> M-x slime-eval-last-expression-display-output|对光标前的表达式求值并将结果打印在显示缓冲区里|
|求值|C-c C-u <br> M-x slime-undefine-function|用fmakunbound来取消当前光标处函数的定义|
|编译|==C-c C-c== <br> M-x slime-compile-defun|编译光标处的top-level形式|
|编译|==C-c C-k== <br> M-x slime-compile-and-load-file|编译和加载当前缓冲区的源文件|
|编译|==C-c M-k== <br> M-x slime-compile-file|编译（但不加载）当前缓冲区的源文件|
|编译|C-c C-l <br> M-x slime-load-file|加载Lisp文件|
|编译|M-x slime-compile-region|编译选中的区域|
|编译消息|M-n <br> M-x slime-next-note|将光标移到下一个编译器消息处并显示消息|
|编译消息|M-p <br> M-x slime-previous-note|将光标移到上一个编译器消息处并显示消息|
|编译消息|C-c M-c <br> M-x slime-remove-notes|删除缓冲区里的所有提示信息|
|编译消息|C-x ‘ <br> M-x next-error|访问下一个错误消息|
|补全|M-TAB <br> M-x slime-complete-symbol|补全光标处的符号|
|查找定义| ==M-.== <br> M-x slime-edit-definition|跳至光标处符号的定义处|
|查找定义| ==M-,== <br> ==M-*== <br> M-x slime-pop-find-definition-stack|回到M-.命令执行的光标处|
|查找定义| ==C-x 5 .== <br> M-x slime-edit-definition-other-frame|类似slime-edit-definition，但是会跳到另一个框架来编辑其定义|
|查找定义|M-x slime-edit-definition-with-etags|使用ETAGES的表来寻找当前光标处的定义|
|文档|C-c C-d d <br> M-x slime-describe-symbol|描述当前光标处的符号|
|文档|C-c C-d f <br> M-x slime-describe-function|描述当前光标处的函数|
|文档|C-c C-d a <br> M-x slime-apropos|对于一个正则表达式执行一个合适的搜索，来搜索所有的Lisp符号名称，并且显示出相应的文档字符串|
|文档|C-c C-d z <br> M-x slime-apropos-all|类似slime-apropos但是默认包含所有内部符号|
|文档|C-c C-d p <br> M-x slime-apropos-package|显示包内所有符号的合适的结果|
|文档|C-c C-d h <br> M-x slime-hyperspec-lookup|在Hyperspec里查找当前光标处的符号|
|文档|C-c C-d ~ <br> M-x hyperspec-lookup-format|在Hyperspec里查找一个foramt格式控制符|
|文档|C-c C-d # <br> M-x hyperspec-lookup-reader-macro|在Hyperspec里查找一个读取宏|
|交叉引用|C-c C-w c <br> M-x slime-who-calls|显示该函数的调用者|
|交叉引用|C-c C-w w <br> M-x slime-calls-who|显示该函数调用了的函数|
|交叉引用|C-c C-w r <br> M-x slime-who-references|显示对全局变量的引用|
|交叉引用|C-c C-w b <br> M-x slime-who-binds|显示对全局标量的绑定|
|交叉引用|C-c C-w s <br> M-x slime-who-sets|显示对全局标量的赋值|
|交叉引用|C-c C-w m <br> M-x slime-who-macroexpands|显示某个宏扩展之后的结果|
|交叉引用|M-x slime-who-specializes|显示一个类所有已知的方法|
|宏扩展|C-c C-m <br> M-x slime-macroexpand-1|将光标处的表达式宏展开一次|
|宏扩展|C-c M-m <br> M-x slime-macroexpand-all|将光标处的表达式完全宏展开|
|宏扩展|M-x slime-compiler-macroexpand-1|显示光标处的编译宏展开的sexp|
|宏扩展|M-x slime-compiler-macroexpand|反复展开光标处的编译宏的sexp|

TODO(zhoujiagen): other commands



## Atom Slime

[SLIMA](https://github.com/neil-lindquist/slima)


```
Keystroke	    Command
alt-.	        slime:goto-definition
alt-c	        slime:compile-function
alt-k	        slime:compile-buffer
alt-m	        slime:macroexpand-1
alt-shift-M     slime:macroexpand-all

ctrl-=	       slime:clear-repl
ctrl-c	       slime:interrupt-lisp
ctrl-cmd-.	   slime:goto-definition
ctrl-cmd-c	   slime:compile-function
ctrl-cmd-k	   slime:compile-buffer
ctrl-cmd-m	   slime:macroexpand-1
ctrl-i	       slime:inspect-presentation
ctrl-shift-cmd-M	slime:macroexpand-all

```

## markdown-mode

```
M-x package-install RET markdown-mode RET
```
