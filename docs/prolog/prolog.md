# Prolog逻辑编程

## 环境

- GNU Prolog: 下载后默认安装在目录`/opt/local/bin/gprolog`
- Emacs: prolog-mode

`~/.bash_profile`(NOT WORK):

``` shell
# Prolog
export PATH=/opt/local/bin:$PATH
export EPROLOG=/opt/local/bin/gprolog
```

`~/.emacs`(WORK):

``` lisp
;;; Prolog
; cannot find program error: https://askubuntu.com/questions/65168/emacs-24-cant-find-ls
; /opt/local/bin/gprolog
(setenv "PATH" (concat (getenv "PATH") ":/opt/local/bin/"))
(setq exec-path (append exec-path '("/opt/local/bin/")))

; https://www.emacswiki.org/emacs/PrologMode
; https://bruda.ca/emacs/prolog_mode_for_emacs
; https://github.com/emacs-mirror/emacs/blob/master/lisp/progmodes/prolog.el
(autoload 'run-prolog "prolog" "Start a Prolog sub-process." t)
(autoload 'prolog-mode "prolog" "Major mode for editing Prolog programs." t)
(autoload 'mercury-mode "prolog" "Major mode for editing Mercury programs." t)
(setq prolog-system 'gnu)
(setq auto-mode-alist (append '(("\\.pl$" . prolog-mode)
                                ("\\.m$" . mercury-mode))
                               auto-mode-alist))
```
