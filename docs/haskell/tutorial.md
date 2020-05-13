# Hashkell Tutorial

> Real World Haskell

## ghci

``` haskell
$ ghci
GHCi, version 8.8.2: https://www.haskell.org/ghc/  :? for help
Prelude> 2 + 2
4
Prelude> 7 / 2
3.5
Prelude> (+) 2 2
4
```

``` haskell
Prelude> True
True
Prelude> False
False
Prelude> True && False
False
Prelude> False || True
True
Prelude> 1 == 1
True
Prelude> 4 >= 3.99
True
Prelude> 2 /= 3
True
Prelude> not True
False
```

``` haskell
Prelude> :info (+)
class Num a where
  (+) :: a -> a -> a
  ...
  	-- Defined in ‘GHC.Num’
infixl 6 +
Prelude> let e = exp 1
Prelude> e
2.718281828459045
```

``` haskell
Prelude> [1, 2, 3]
[1,2,3]
Prelude> ["one", "two", "three"]
["one","two","three"]
Prelude> [1..10]
[1,2,3,4,5,6,7,8,9,10]
Prelude> [1.0, 1.25..2.0]
[1.0,1.25,1.5,1.75,2.0]
Prelude> [10,8..1]
[10,8,6,4,2]
Prelude> [3, 1, 3] ++ [3, 7]
[3,1,3,3,7]
Prelude> [] ++ [True, False] ++ [True]
[True,False,True]
Prelude> 1: [2, 3]
[1,2,3]
Prelude> 1 : []
[1]
```

``` haskell
Prelude> "This is a string."
"This is a string."
Prelude> putStrLn "Here's a newline -->\n<-- See?"
Here's a newline -->
<-- See?
Prelude> let a = ['h', 'e', 'l', 'l', 'o']
Prelude> a
"hello"
Prelude> a == "hello"
True
Prelude> a == "Hello"
False
Prelude> "" == []
True
Prelude> 'a' : "bc"
"abc"
Prelude> "foo" ++ "bar"
"foobar"
```

``` haskell
Prelude> :set +t
```

``` haskell
Prelude> 'c'
'c'
it :: Char
Prelude> "foo"
"foo"
it :: [Char]
Prelude> it
"foo"
it :: [Char]
```

``` haskell
Prelude> 11 % 29

<interactive>:44:4: error:
    Variable not in scope: (%) :: Integer -> Integer -> t
Prelude> :m Data.Ratio
Prelude Data.Ratio> :module Data.Ratio
Prelude Data.Ratio> 11 % 29
11 % 29
it :: Integral a => Ratio a
Prelude Data.Ratio> :m - Data.Ratio
Prelude> 2
2
```

``` haskell
Prelude Data.Ratio> :unset +t
Prelude Data.Ratio> 2
2
```

``` haskell
Prelude Data.Ratio> :type 'a'
'a' :: Char
Prelude Data.Ratio> "foo"
"foo"
Prelude Data.Ratio> :type it
it :: [Char]
```


## Types

Haskell中每个表达式和函数均有类型.

Types in Hashell are ==strong==, ==static==, and can be ==automatically inferred==.

### 基础类型

- `Char`
- `Bool`: `True`, `False`
- `Int`: 32位或64位有符号整数
- `Integer`: 有符号整数
- `Double`

显式指定类型: `expression :: MyType`, 例如`'a' :: Char`

### 列表

列表中元素必须是相同的类型.

``` haskell
Prelude> [1,2,3]
[1,2,3]
Prelude> :type [1,2,3]
[1,2,3] :: Num a => [a]
Prelude> :type [[True], [False, 2 >= 3]]
[[True], [False, 2 >= 3]] :: [[Bool]]
```

### 元组

元组是固定大小的一组值, 每个值可以有不同的类型.

``` haskell
Prelude> (1964, "Labyrinths")
(1964,"Labyrinths")
Prelude> :type it
it :: Num a => (a, [Char])

Prelude> :type (4, ['a', 'm'], (16, True))
(4, ['a', 'm'], (16, True))
  :: (Num a1, Num a2) => (a1, [Char], (a2, Bool))
```

特殊的类型: `()`, 表示零个元素的元组, 该类型有唯一值`()`.

``` haskell
Prelude> ()
()
Prelude> :type ()
() :: ()
```

### 定义类型

``` haskell
data BookInfo = Book Int String [String]
                deriving (Show)
```

其中:

- `BookInfo`: 类型构造器
- `Book`: 值构造器/数据构造器
- `Int String [String]`是类型的组件

### 代数数据类型

``` haskell
type CardNumber = String
type CardHolder = String
type Address = [String]
data BillingInfo = CreaditCard CardNumber CardHolder Address
                 | CashOnDelivery
                 | Invoice CustomerID
                   deriving (Show)
```

``` haskell
*Main> :info BillingInfo
data BillingInfo
  = CreaditCard CardNumber CardHolder Address
  | CashOnDelivery
  | Invoice CustomerID
  	-- Defined at BookStore.hs:43:1
instance [safe] Show BillingInfo -- Defined at BookStore.hs:46:30
*Main> :type CashOnDelivery
CashOnDelivery :: BillingInfo
*Main> :type Invoice
Invoice :: CustomerID -> BillingInfo
*Main> :type CreaditCard
CreaditCard :: CardNumber -> CardHolder -> Address -> BillingInfo
*Main> :type Invoice 1234
Invoice 1234 :: BillingInfo
```

### 记录语法

定义:

``` haskell
data Customer = Customer {
  customerID :: CustomerID,
  customerName :: String,
  customerAddress :: Address
} deriving (Show)
```

构造实例:

``` haskell
customer1 = Customer 281828 "J.R. Hacker"
            ["255 Syntax Ct", "Milpitas, CA 95134", "USA"]
customer2 = Customer {
  customerID = 271828,
  customerAddress = ["1048576 Disk Drive", "Milpitas, CA 95134", "USA"],
  customerName = "Jane Q. Citizen"
}
```

访问字段:

``` haskell
*Main> customerName customer1
"J.R. Hacker"
*Main> customerName customer2
"Jane Q. Citizen"
```

### 参数化类型

``` haskell
-- a是类型变量
data MyMaybe a = MyJust a
               | MyNothing
                 deriving (Show)

someBool = MyJust True
someString = MyJust "something"
```

``` haskell
*Main> MyJust 1.5
MyJust 1.5
*Main> MyNothing
MyNothing
*Main> :type MyJust 1.5
MyJust 1.5 :: Fractional a => MyMaybe a
```

### 递归类型

列表:

``` haskell
data List a = Cons a (List a)
            | Nil
              deriving (Show)

list = Cons 3 (Cons 2 (Cons 1 (Cons 0 Nil)))

*Main> list
Cons 3 (Cons 2 (Cons 1 (Cons 0 Nil)))
*Main> :type list
list :: List Integer
```

树:

``` haskell
data Tree a = Node a (Tree a) (Tree a)
          | Empty
            deriving (Show)

simpleTree = Node "parent" (Node "left child" Empty Empty) (Node "rigth child" Empty Empty)

*Main> simpleTree
Node "parent" (Node "left child" Empty Empty) (Node "rigth child" Empty Empty)
*Main> :type simpleTree
simpleTree :: Tree [Char]
```

## Expressions

Haskell是一个面向表达式的语言.

在Haskell中, 变量提供了给一个表达式命名的方式, 一旦变量绑定到一个表达式, 它的值不再改变.

`if`表达式:

``` haskell
myDrop n xs = if n <= 0 || null xs
              then xs
              else myDrop (n-1) (tail xs)
```

## Functions

在Haskell中, 函数应用是左结合的.


函数的类型: `->`指出返回值的类型.

``` haskell
Prelude> lines "the quick\nbrown fox\njumps"
["the quick","brown fox","jumps"]
Prelude> :type lines
lines :: String -> [String]
```

有副作用的函数: 函数的返回值类型以`IO`开始.

``` haskell
Prelude> :type readFile
readFile :: FilePath -> IO String
```

定义函数: `=`的左侧是函数名称和参数, 右侧是函数体.

``` haskell
add a b = a + b
```

### 多态函数

``` haskell
Prelude> last [1,2,3,4]
4
Prelude> :type last
last :: [a] -> a
```

### 报告错误

``` haskell
mySecond :: [a] -> a
mySecond xs = if null (tail xs)
              then error "list too short"
              else head (tail xs)
```


### 局部变量

``` haskell
lend amount balance = let reserve = 100
                          newBalance = balance - amount
                      in if balance < reserve
                         then Nothing
                         else Just newBalance
```


内嵌:

``` haskell
Prelude> :set +m

Prelude> foo = let a = 1
Prelude|     in let b = 2
Prelude|         in a + b

Prelude> foo
3
```

遮盖:

``` haskell
Prelude> bar = let x = 1
Prelude|     in ((let x = "foo" in x), x)
Prelude> bar
("foo",1)

Prelude> quux a = let a= "foo" in a ++ "eek!"
Prelude> :type quux
quux :: p -> [Char]
```

使用`where`:

``` haskell
lend2 amount balance = if amount < reserve * 0.5
                        then Just newBalance
                        else Nothing
      where reserve = 100
            newBalance = balance - amount
```

### 局部函数

``` haskell
pluralise :: String -> [Int] -> [String]
pluralise word counts = map plural counts
  where plural 0 = "no " ++ word ++ "s"
        plural 1 = "one " ++ word
        plural n = show n ++ " " ++ word ++ "s"
```

``` haskell
*Main> pluralise "apple" [1,2,3]
["one apple","2 apples","3 apples"]
```

### 全局变量

``` haskell
-- file: ch03/GlobalVariables.hs

itemName = "Weighted Companion Cube"

*Main> itemName
"Weighted Companion Cube"
```
