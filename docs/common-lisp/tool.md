# Common Lisp Tools

[Development tools of CLiki](https://www.cliki.net/Development)

## Editors

[The Common Lisp Cookbook – Editor support](https://lispcookbook.github.io/cl-cookbook/editor-support.html)

## LispWorks

http://www.lispworks.com/downloads/index.html

## Portacle

[Home](https://portacle.github.io/)

The following software packages are included in Portacle:

+ Emacs
+ SBCL
+ Quicklisp
+ Git

无法打开的情况处理: [Portacle Does Not Launch on MacOS Sierra 10.12.1](https://github.com/portacle/portacle/issues/53)


## Roswell

> [Roswell - Common Lisp environment setup Utility.](https://github.com/roswell/roswell)
>
> Roswell is a Lisp implementation installer/manager, launcher, and much more!

## SBCL

> Steel Bank Common Lisp

+ [Home](https://github.com/sbcl/sbcl)


安装:

``` lisp
$ brew install sbcl
$ brew install rlwrap
$ which sbcl
/usr/local/bin/sbcl
$ sbcl --version
SBCL 1.5.5

$ rlwrap sbcl
This is SBCL 1.5.5, an implementation of ANSI Common Lisp.
More information about SBCL is available at <http://www.sbcl.org/>.

SBCL is free software, provided as is, with absolutely no warranty.
It is mostly in the public domain; some portions are provided under
BSD-style licenses.  See the CREDITS and COPYING files in the
distribution for more information.

> (+ 1 1)
2
> (sb-ext:exit)
```


修改当前目录:

``` lisp
(sb-posix:getcwd)
(sb-posix:chdir "/tmp/")
```

使用`*default-pathname-defaults*`的例子见[Prove](#Prove).

## Quicklisp

> a library manager for Common Lisp

[Home](https://www.quicklisp.org)


``` lisp
$ rlwrap sbcl --load quicklisp.lisp
> (quicklisp-quickstart:install)

> (ql:system-apropos "vecto")
#<SYSTEM 3d-vectors / 3d-vectors-20190710-git / quicklisp 2019-07-11>
#<SYSTEM 3d-vectors-test / 3d-vectors-20190710-git / quicklisp 2019-07-11>
#<SYSTEM adw-charting-vecto / adw-charting-20120909-http / quicklisp 2019-07-11>
#<SYSTEM cl-aa / cl-vectors-20180228-git / quicklisp 2019-07-11>
#<SYSTEM cl-aa-misc / cl-vectors-20180228-git / quicklisp 2019-07-11>
#<SYSTEM cl-glfw-opengl-apple_specular_vector / cl-glfw-20150302-git / quicklisp 2019-07-11>
#<SYSTEM cl-paths / cl-vectors-20180228-git / quicklisp 2019-07-11>
#<SYSTEM cl-paths-ttf / cl-vectors-20180228-git / quicklisp 2019-07-11>
#<SYSTEM cl-vectors / cl-vectors-20180228-git / quicklisp 2019-07-11>
#<SYSTEM com.elbeno.vector / vector-20130128-git / quicklisp 2019-07-11>
#<SYSTEM lispbuilder-sdl-cl-vectors / lispbuilder-20190521-git / quicklisp 2019-07-11>
#<SYSTEM lispbuilder-sdl-cl-vectors-examples / lispbuilder-20190521-git / quicklisp 2019-07-11>
#<SYSTEM lispbuilder-sdl-vecto / lispbuilder-20190521-git / quicklisp 2019-07-11>
#<SYSTEM lispbuilder-sdl-vecto-examples / lispbuilder-20190521-git / quicklisp 2019-07-11>
#<SYSTEM sb-vector-io / sb-vector-io-20110829-git / quicklisp 2019-07-11>
#<SYSTEM static-vectors / static-vectors-v1.8.3 / quicklisp 2019-07-11>
#<SYSTEM static-vectors/test / static-vectors-v1.8.3 / quicklisp 2019-07-11>
#<SYSTEM vecto / vecto-1.5 / quicklisp 2019-07-11>
#<SYSTEM vecto-stuff / sucle-20190521-git / quicklisp 2019-07-11>
#<SYSTEM vectometry / vecto-1.5 / quicklisp 2019-07-11>
#<SYSTEM vectors / vectors-20171227-git / quicklisp 2019-07-11>


> (ql:quickload "vecto")
To load "vecto":
  Load 1 ASDF system:
    asdf
  Install 5 Quicklisp releases:
    cl-vectors salza2 vecto zpb-ttf zpng
; Fetching #<URL "http://beta.quicklisp.org/archive/salza2/2013-07-20/salza2-2.0.9.tgz">
; 15.16KB
==================================================
15,525 bytes in 0.00 seconds (0.00KB/sec)
; Fetching #<URL "http://beta.quicklisp.org/archive/zpng/2015-04-07/zpng-1.2.2.tgz">
; 39.20KB
==================================================
40,141 bytes in 0.01 seconds (5600.03KB/sec)
; Fetching #<URL "http://beta.quicklisp.org/archive/zpb-ttf/2013-07-20/zpb-ttf-1.0.3.tgz">
; 43.82KB
==================================================
44,869 bytes in 0.32 seconds (137.79KB/sec)
; Fetching #<URL "http://beta.quicklisp.org/archive/cl-vectors/2018-02-28/cl-vectors-20180228-git.tgz">
; 30.68KB
==================================================
31,415 bytes in 0.38 seconds (81.16KB/sec)
; Fetching #<URL "http://beta.quicklisp.org/archive/vecto/2017-12-27/vecto-1.5.tgz">
; 69.10KB
==================================================
70,758 bytes in 0.17 seconds (418.79KB/sec)
; Loading "vecto"
[package net.tuxee.aa]............................
[package net.tuxee.aa-bin]........................
[package net.tuxee.paths].........................
[package net.tuxee.vectors].......................
[package salza2]..................................
[package zpng]....................................
[package zpb-ttf].................................
[package vecto]........
("vecto")
```

``` lisp
> (ql:add-to-init-file)
I will append the following lines to #P"/Users/.../.sbclrc":

  ;;; The following lines added by ql:add-to-init-file:
  #-quicklisp
  (let ((quicklisp-init (merge-pathnames "quicklisp/setup.lisp"
                                         (user-homedir-pathname))))
    (when (probe-file quicklisp-init)
      (load quicklisp-init)))

Press Enter to continue.

```

``` lisp
(ql:update-dist "quicklisp")
(ql:update-client)
```



## ASDF

> Another System Definition Facility

Another System Definition Facility is the de facto standard build facility for Common Lisp. Your Lisp implementation probably contains a copy of ASDF, which you can load using `(require "asdf")`.

``` lisp
* (require :asdf)
("ASDF" "asdf" "UIOP" "uiop")
* (asdf:asdf-version)
"3.3.1"
```

默认的system查找目录: `~/common-lisp/`

定制system查找目录: 在`~/.config/common-lisp/source-registry.conf.d/`创建`xxx.conf`文件, 其内容为
`(:tree "/your-direcotry/")`

加载system:

``` lisp
(asdf:load-system "my-system")
```


例: `foo.asd`

``` lisp
(in-package :asdf-user)
(defsystem "foo"
    :version (:read-file-form "variables" :at (3 2))
    :components
    ((:file "package")
     (:file "variables" :depends-on ("package"))
     (:module "mod"
	      :depends-on ("package")
	      :serial t
	      :components ((:file "utils")
			   (:file "reader")
			   (:file "cooker")
			   (:static-file "data.raw"))
	      :output-files (compile-op (o c) (list "data.cooked"))
	      :perform (compile-op :after (o c)
				   (cook-data
				    :in (component-pathname (find-component c "data.raw"))
				    :out (first (output-files o c)))))
     (:file "foo" :depends-on ("mod"))))
(defmethod action-description
    ((o compile-op) (c (eql (find-component "foo" "mod"))))
  "cooking data")
```

`defsystem`的语法:

``` EBNF
system-definition := ( defsystem system-designator system-option* )

(* 系统标识 *)
system-designator := simple-component-name
                    | complex-component-name
(* see [Simple component names], page 18, NOTE: Underscores are not permitted. *)
simple-component-name := lower-case string | symbol
(* see [Complex component names], page 19, 支持在一个.asd文件中放置多个系统 *)
complex-component-name := string | symbol

(* 系统选项 *)
system-option := :defsystem-depends-on system-list (* 定义系统间依赖关系 *)
                | :weakly-depends-on system-list
                | :class class-name (* 系统类, see [System class names], page 19 *)
                | :build-pathname pathname-specifier
                | :build-operation operation-name
                | system-option/asdf3
                | module-option
                | option
(* ASDF3中系统选项: These are only available since ASDF 3 (actually its alpha release 2.27) *)
system-option/asdf3 := :homepage string
                      | :bug-tracker string
                      | :mailto string
                      | :long-name string
                      | :source-control source-control
                      | :version version-specifier
                      | :entry-point object (* 指定可执行程序的入口点, see [Entry point], page 24 *)
(* 源码控制 *)
source-control := ( keyword string )
(* 模块选项 *)
module-option := :components component-list
                | :serial [ t | nil ]
(* 具体的选项 *)
option := :description string
          | :long-description string
          | :author person-or-persons
          | :maintainer person-or-persons
          | :pathname pathname-specifier (* 代码位置 *)
          | :default-component-class class-name
          | :perform method-form
          | :explain method-form
          | :output-files method-form
          | :operation-done-p method-form
          | :if-feature feature-expression (* 类似于#+ *)
          | :depends-on ( dependency-def* )
          | :in-order-to ( dependency+ )

person-or-persons := string | ( string+ )

system-list := ( simple-component-name* )

component-list := ( component-def* )
(* 组件定义 *)
component-def := ( component-type simple-component-name option* )
(* 组件类型, see [Component types], page 19 *)
component-type := :module | :file | :static-file | other-component-type
other-component-type := symbol-by-name

(* :depends-on中依赖定义 *)
(* This is used in :depends-on, as opposed to "dependency", which is used in :in-order-to *)
dependency-def := simple-component-name
                  | ( :feature feature-expression dependency-def ) (* 按特性定义依赖, see [Feature dependencies], page 22 *)
                  | ( :version simple-component-name version-specifier )
                  | ( :require module-name )

(* :in-order-to中依赖定义 *)
(* "dependency" is used in :in-order-to, as opposed to "dependency-def" *)
dependency := ( dependent-op requirement+ )
requirement := ( required-op required-component+ )
dependent-op := operation-name
required-op := operation-name

(* 路径名描述符 *)
(* NOTE: pathnames should be all lower case, and have no underscores, although hyphens are permitted. *)
pathname-specifier := pathname | string | symbol

(* 版本描述符. *)
version-specifier := string
                    | ( :read-file-form pathname-specifier form-specifier? )
                    | ( :read-file-line pathname-specifier line-specifier? )
line-specifier := :at integer (* base zero *)
form-specifier := :at [ integer | ( integer+ ) ]

(* 方法形式 *)
method-form := ( operation-name qual lambda-list &rest body )
qual := method-qualifier?
method-qualifier := :before | :after | :around

(* 特性表达式 *)
feature-expression := keyword
                      | ( :and feature-expression* )
                      | ( :or feature-expression* )
                      | ( :not feature-expression )

(* 操作名 *)
operation-name := symbol
```

## SQL

### CLSQL

> A multi-platform SQL interface for Common Lisp

[CLSQL Users' Guide](http://clsql.kpe.io/manual/)

安装:
``` shell
brew install mysql-connector-c
```

``` lisp
(ql:quickload :clsql)
```

建立连接:
``` lisp
> (connected-databases)
NIL
> (database-name-from-spec '("127.0.0.1" "movies" "root" "admin") :mysql)
"127.0.0.1/movies/root"
> (connect '("127.0.0.1" "movies" "root" "admin") :database-type :mysql)
#<CLSQL-MYSQL:MYSQL-DATABASE 127.0.0.1/movies/root OPEN {1004683993}>
> (connected-databases)
(#<CLSQL-MYSQL:MYSQL-DATABASE 127.0.0.1/movies/root OPEN {1004683993}>)
> (disconnect)
T
> (connected-databases)
NIL

> (connect '("127.0.0.1" "movies" "root" "admin") :database-type :mysql)
#<CLSQL-MYSQL:MYSQL-DATABASE 127.0.0.1/movies/root OPEN {10046878B3}>
```

执行查询:
``` lisp
> (query "SELECT  ORDINAL_POSITION, COLUMN_NAME, COLUMN_TYPE, IS_NULLABLE, COLUMN_DEFAULT, COLUMN_COMMENT FROM information_schema.COLUMNS WHERE TABLE_SCHEMA = 'movies' AND TABLE_NAME = 'Movies' ORDER BY ORDINAL_POSITION;")
((1 "title" "varchar(100)" "NO" NIL "") (2 "year" "int(11)" "NO" NIL "")
 (3 "length" "int(11)" "YES" NIL "") (4 "genre" "varchar(10)" "YES" NIL "")
 (5 "studioName" "varchar(30)" "YES" NIL "")
 (6 "producerC#" "int(11)" "YES" NIL ""))
("ORDINAL_POSITION" "COLUMN_NAME" "COLUMN_TYPE" "IS_NULLABLE" "COLUMN_DEFAULT"
 "COLUMN_COMMENT")

> (query "SELECT * FROM Movies;")
NIL
("title" "year" "length" "genre" "studioName" "producerC#")
```

关闭连接:
``` lisp
> (connected-databases)
(#<CLSQL-MYSQL:MYSQL-DATABASE 127.0.0.1/movies/root OPEN {10047F2453}>)
> (find-database "127.0.0.1/movies/root")
#<CLSQL-MYSQL:MYSQL-DATABASE 127.0.0.1/movies/root OPEN {10047F2453}>
1
> (disconnect :database (find-database "127.0.0.1/movies/root"))
T
> (find-database "127.0.0.1/movies/root")

debugger invoked on a SQL-DATABASE-ERROR in thread
#<THREAD "main thread" RUNNING {10004F84C3}>:
  A database error occurred: NIL / NIL
  There exists no database called 127.0.0.1/movies/root.

Type HELP for debugger help, or (SB-EXT:EXIT) to exit from SBCL.

restarts (invokable by number or by possibly-abbreviated name):
  0: [CONTINUE] Return nil.
  1: [ABORT   ] Exit debugger, returning to top level.

(SB-INT:SIMPLE-EVAL-IN-LEXENV (FIND-DATABASE "127.0.0.1/movies/root") #<NULL-LEXENV>)
0] 0
NIL
> (connected-databases)
NIL
```

### cl-dbi

> [Database independent interface for Common Lisp](https://github.com/fukamachi/cl-dbi)

安装:

``` lisp
(ql:quickload :cl-dbi)
```

简单使用:

``` lisp
(defvar *connection*
  (dbi:connect :mysql
               :host "127.0.0.1"
               :port 3306
               :database-name "movies"
               :username "root"
               :password "admin"))
*CONNECTION*
```

``` lisp
(let* ((query (dbi:prepare *connection*
                           "SELECT * FROM Movies WHERE `producerC#` = ?"))
       (query (dbi:execute query 1)))
  (loop for row = (dbi:fetch query)
        while row
        ;; process "row".
        do (print row)))

(:|title| "title" :|year| 2000 :|length| 120 :|genre| "genre" :|studioName|
 "studioName" :|producerC#| 1)
NIL
```

``` lisp
> (dbi:fetch-all (dbi:execute (dbi:prepare *connection* "SELECT * FROM Movies WHERE `producerC#` = ?")
                            1))
((:|title| "title" :|year| 2000 :|length| 120 :|genre| "genre" :|studioName|
  "studioName" :|producerC#| 1))
```



## CL-Project


[CL-Project - Generate modern project skeletons](https://github.com/fukamachi/cl-project)

``` lisp
> (ql:quickload :cl-project)
To load "cl-project":
  Load 4 ASDF systems:
    asdf cl-fad prove uiop
  Install 4 Quicklisp releases:
    cl-emb cl-ppcre cl-project local-time
...
```

``` lisp
(cl-project:make-project #p"lib/cl-sample/"
  :author "Eitaro Fukamachi"
  :email "e.arrows@gmail.com"
  :license "LLGPL"
  :depends-on '(:clack :cl-annot))
writing lib/cl-sample/cl-sample.asd
writing lib/cl-sample/README.org
writing lib/cl-sample/README.markdown
writing lib/cl-sample/.gitignore
writing lib/cl-sample/src/main.lisp
writing lib/cl-sample/tests/main.lisp
T
```

``` shell
$ tree demo-cl-project/
demo-cl-project/
└── lib
    └── cl-sample
        ├── README.markdown
        ├── README.org
        ├── cl-sample.asd
        ├── src
        │   └── main.lisp
        └── tests
            └── main.lisp

4 directories, 5 files
```

## Test

### <span id="Prove">Prove</span>

[Yet another unit testing framework for Common Lisp](https://github.com/fukamachi/prove)

``` lisp
> (ql:quickload :prove)
```

demo-prove.lisp:

``` lisp
(in-package :cl-user)
(defpackage my-test
  (:use :cl
        :prove))
(in-package :my-test)

(plan 3)

(ok (not (find 4 '(1 2 3))))
(is 4 4)
(isnt 1 #\1)

(finalize)
```

``` lisp
> (prove:run #P"/.../demo-prove.lisp")
1..3

  ✓ T is expected to be T

  ✓ 4 is expected to be 4

  ✓ 1 is not expected to be #\1

✓ 3 tests completed (0ms)
T
(#P"/.../demo-prove.lisp")
NIL
```

``` lisp
> (prove:run #P"/.../demo-prove.lisp" :reporter :fiveam)
...
 Did 3 checks.
    Pass: 3 (100%)
    Fail: 0 (  0%)
T
(#P"/.../demo-prove.lisp")
NIL
>

```

使用`*default-pathname-defaults*`:

``` lisp
> (let ((*default-pathname-defaults* #p"/.../")) (prove:run #p"demo-prove.lisp"))
1..3

  ✓ T is expected to be T

  ✓ 4 is expected to be 4

  ✓ 1 is not expected to be #\1

✓ 3 tests completed (0ms)
T
(#P"demo-prove.lisp")
NIL
>
```



### Rove

[#1=(yet another . #1#) common lisp testing library](https://github.com/fukamachi/rove)

> Rove is a unit testing framework for Common Lisp applications. This is intended to be a successor of Prove.


## Threads

[The Common Lisp Cookbook – Threads](https://lispcookbook.github.io/cl-cookbook/process.html)

Bordeaux:


``` lisp
> (member :thread-support *FEATURES*)
NIL
> (ql:quickload :bt-semaphore)
To load "bt-semaphore":
  Load 2 ASDF systems:
    asdf bordeaux-threads
  Install 1 Quicklisp release:
    bt-semaphore
; Fetching #<URL "http://beta.quicklisp.org/archive/bt-semaphore/2018-07-11/bt-semaphore-20180711-git.tgz">
; 4.09KB
==================================================
4,185 bytes in 0.00 seconds (0.00KB/sec)
; Loading "bt-semaphore"
[package bt-semaphore]
(:BT-SEMAPHORE)
> (member :thread-support *FEATURES*)
(:THREAD-SUPPORT :QUICKLISP :SB-BSD-SOCKETS-ADDRINFO :ASDF3.3 :ASDF3.2 :ASDF3.1
 :ASDF3 :ASDF2 :ASDF :OS-MACOSX :OS-UNIX :NON-BASE-CHARS-EXIST-P :ASDF-UNICODE
 :X86-64 :64-BIT :64-BIT-REGISTERS :ALIEN-CALLBACKS :ANSI-CL :AVX2 :BSD
 :C-STACK-IS-CONTROL-STACK :CALL-SYMBOL :COMMON-LISP :COMPACT-INSTANCE-HEADER
 :COMPARE-AND-SWAP-VOPS :CYCLE-COUNTER :DARWIN :FP-AND-PC-STANDARD-SAVE :GENCGC
 :IEEE-FLOATING-POINT :IMMOBILE-CODE :IMMOBILE-SPACE :INODE64 :INTEGER-EQL-VOP
 :LINKAGE-TABLE :LITTLE-ENDIAN :MACH-EXCEPTION-HANDLER :MACH-O
 :OS-PROVIDES-BLKSIZE-T :OS-PROVIDES-DLADDR :OS-PROVIDES-DLOPEN
 :OS-PROVIDES-PUTWC :OS-PROVIDES-SUSECONDS-T :PACKAGE-LOCAL-NICKNAMES
 :RELOCATABLE-HEAP :SB-CORE-COMPRESSION :SB-DOC :SB-EVAL :SB-LDB
 :SB-PACKAGE-LOCKS :SB-SIMD-PACK :SB-SIMD-PACK-256 :SB-SOURCE-LOCATIONS
 :SB-THREAD :SB-UNICODE :SBCL :STACK-ALLOCATABLE-CLOSURES
 :STACK-ALLOCATABLE-FIXED-OBJECTS :STACK-ALLOCATABLE-LISTS
 :STACK-ALLOCATABLE-VECTORS :STACK-GROWS-DOWNWARD-NOT-UPWARD
 :UNDEFINED-FUN-RESTARTS :UNIX :UNWIND-TO-FRAME-AND-CALL-VOP)
 > bt:*supports-threads-p*
 T
 >
```

## Web Application


### AllegroServe

> [AllegroServe, a web server written in Common Lisp](https://github.com/franzinc/aserve)


### Caveman2

> [Caveman2 - Lightweight web application framework](https://github.com/fukamachi/caveman)

安装, 创建模板项目:

``` lisp
> (sb-posix:getcwd)
"/Users/zhoujiagen/workspace/polyglot-languages/common-lisp/web-app/caveman2"

(ql:quickload :caveman2)

(caveman2:make-project #P"demo-web"
                       :author "zhoujiagen")
```

添加ASDF配置文件`~/.config/common-lisp/source-registry.conf.d/caveman2.conf`:

``` lisp
(:tree "/Users/zhoujiagen/workspace/polyglot-languages/common-lisp/web-app/caveman2/")
```

定制:

- `src/config.lisp`

``` lisp

(defconfig :common
    `(:databases
      ((:maindb :mysql
		:host "127.0.0.1"
		:port 3306
		:database-name "movies"
		:username "root"
		:password "*****"))))

(defconfig |development|
    '(:databases
      ((:maindb :mysql
	:host "127.0.0.1"
	:port 3306
	:database-name "movies"
	:username "root"
	:passowrd "*****"))))
```

- `src/db.lisp`

``` lisp
(:export ...
  :find-movies-from-db)

(defun find-movies-from-db (|id|)
  (with-connection (db)
    (let* ((query (dbi:prepare *connection*
                               "SELECT * FROM Movies WHERE `producerC#` = ?"))
	   (query (dbi:execute query |id|))
	   (result nil))
      (loop for row = (dbi:fetch query)
         while row
         do (push row result))
      result)))
```

- `src/web.lisp`

``` lisp
(defroute "/user.json" (&key |id|)
  (let ((movies (find-movies-from-db |id|)))
    (render-json (car movies))))
```

启动:

``` lisp
(asdf:load-system "demo-web")
(demo-web:start :port 8080)
(demo-web:stop)
```

输出:

```
http://localhost:8080/user.json?id=1
{"title":"title","year":2000,"length":120,"genre":"genre","studioName":"studioName","producerC#":1}

http://localhost:8080/user.json?id=2
null
```

``` lisp
Hunchentoot server is started.
Listening on 127.0.0.1:8080.
#S(CLACK.HANDLER::HANDLER
   :SERVER :HUNCHENTOOT
   :ACCEPTOR #<SB-THREAD:THREAD "clack-handler-hunchentoot" RUNNING
                {100433C8E3}>)
> 127.0.0.1 - [03/Sep/2019:11:04:29 +08:00] "GET /user.json?id=1 HTTP/1.1" 200 99 "-" "-"
127.0.0.1 - [03/Sep/2019:11:04:39 +08:00] "GET /user.json?id=2 HTTP/1.1" 200 4 "-" "-"
127.0.0.1 - [03/Sep/2019:11:04:45 +08:00] "GET /user.json?id=1 HTTP/1.1" 200 99 "-" "-"
```

### Clack

> [Clack - Web Application Environment for Common Lisp](https://github.com/fukamachi/clack)
