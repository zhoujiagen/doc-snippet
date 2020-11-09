# Git


- recover a dropped stash

``` shell
$ git fsck --no-reflo
Checking object directories: 100% (256/256), done.
Checking objects: 100% (428/428), done.
...
dangling commit 49ebfb61a2fdd489a7a605311d03affa2224b0e1
...

$ git log --graph --oneline --decorate 49ebfb61a2fdd489a7a605311d03affa2224b0e1

$ git stash apply 49ebfb61a2fdd489a7a605311d03affa2224b0e1
```

- [Squash my last X commits together using Git](https://stackoverflow.com/questions/5189560/squash-my-last-x-commits-together-using-git)

``` shell
$ git rebase -i <after-this-commit>
# replace "pick" on the second and subsequent commits with "squash" or "fixup"

$ git push -f
```
