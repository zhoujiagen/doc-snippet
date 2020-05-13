# Jupyter

## [Jupyter kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)


## [jupyter_contrib_nbextensions](https://github.com/ipython-contrib/jupyter_contrib_nbextensions):

```
pip install jupyter_contrib_nbextensions
// conda install -c conda-forge jupyter_contrib_nbextensions

jupyter nbextension enable <nbextension require path>
jupyter nbextension disable <nbextension require path>
```

## [jupyter-black](https://github.com/drillan/jupyter-black): 格式化器

``` shell
pip install black
jupyter nbextension install https://github.com/drillan/jupyter-black/archive/master.zip --user
jupyter nbextension enable jupyter-black-master/jupyter-black
```

## [jupyter-themes](https://github.com/dunovank/jupyter-themes)

``` shell
pip install jupyterthemes
jt -l
jt -t chesterish -f ubuntu -nf ubuntu -tf ubuntu -fs 13 -nfs 13 -ofs 12 -tfs 12 -lineh 150 -T -kl
```

`~/.jupyter/custom/custom.css`:

``` css
div.output_subarea {
  ...
  padding: 0em !important;
  ...
}
```

## Common Lisp

- [cl-jupyter](https://github.com/fredokun/cl-jupyter)
- [common-lisp-jupyter](https://github.com/yitzchak/common-lisp-jupyter)

```
$ brew install zmq
```

``` lisp
(ql:quickload :common-lisp-jupyter)
(cl-jupyter:install)
```
