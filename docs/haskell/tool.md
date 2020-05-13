# Hashkell Tools

## Documentation

- [Haskell 2010 Language Report](https://www.haskell.org/onlinereport/haskell2010/)
- [Haskell wiki](https://wiki.haskell.org/Haskell)

## Build

- [The Haskell Tool Stack](https://docs.haskellstack.org/en/stable/README/): Stack is a cross-platform program for developing Haskell projects.
- [Cabal](https://www.haskell.org/cabal/users-guide/intro.html): Cabal is the standard package system for Haskell software.

## Compile

- [GHC: The Glasgow Haskell Compiler](https://www.haskell.org/ghc/)
- [Glasgow Haskell Compiler User's Guide](https://downloads.haskell.org/ghc/latest/docs/html/users_guide/)
- [Haskell Hierarchical Libraries](https://downloads.haskell.org/~ghc/8.8.2/docs/html/libraries/index.html)
- [The GHC API](https://downloads.haskell.org/~ghc/8.8.2/docs/html/libraries/ghc-8.8.2/index.html)

### ghci

- 加载模块: `:module + Data.Ratio`, `:m +Data.Ratio`
- 查看操作符: `:info (+)`
- 显示表达式的类型信息: `:set +t`, `:unset +t`
- 查看表达式的类型信息: `:type 'a'`
- 最后一个表达式的求值结果: `it`
- 加载源文件: 加载`add.hs`, `:load add.hs`
- 查看模块内容: `:browse Prelude`

## Packages

- [Hackage](https://hackage.haskell.org/): Hackage is the Haskell community's central package archive of open source software.
- [QuickCheck](https://hackage.haskell.org/package/QuickCheck): QuickCheck is a library for random testing of program properties.
