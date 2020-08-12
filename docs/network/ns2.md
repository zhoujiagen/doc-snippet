# NS2


## 资源

- Teerawat Issariyakul, Ekram Hossain. Introduction to Network Simulator NS2, Second Edition. Springer: 2012.

- Mac OS 10.13.3安装

参考: [Error while installing NS2.35 to Mac OS(Lion)](http://network-simulator-ns-2.7690.n7.nabble.com/Error-while-installing-NS2-35-to-Mac-OS-Lion-td8359.html), [Ns2安装详细教程(Ubuntu)以及常见问题2019](https://blog.csdn.net/hhhhh2333/article/details/89223984)

补丁: [#21 install patch for OS X Mountain Lion](http://sourceforge.net/p/nsnam/patches/21/)

```
cd ns-allinone-2.35
patch -p1 -i install.osx.patch
./install
```
