# 库

- The Haskell 2010 Report
- Thinking Functionally with Haskell

## 简化的定义

``` haskell
(&&) :: Bool -> Bool -> Bool

class Eq a where
  (==), (/=) :: a -> a -> Bool
  x /= y = not (x == y)

class (Eq a) => Ord a where
  (<),(<=),(>=),(>) :: a -> a -> Bool
  x < y  = not (x >= y)
  x <= y = x == y || x < y
  x >= y = x == y || x > y
  x > y  = not (x < y)

until :: (a -> Bool) -> (a -> a) -> a -> a
until p f x = if p x then x else until p f (f x)

data Nat = Zero | Succ Nat deriving (Eq,Ord,Show)
```


``` haskell
iterate :: (a -> a) -> a -> [a]
iterate f x = x:iterate f (f x)

null :: [a] -> Bool
head :: [a] -> a
tail :: [a] -> [a]
last :: [a] -> a

(++) :: [a] -> [a] -> [a]

concat :: [[a]] -> [a]
map :: (a -> b) -> [a] -> [b]
filter :: (a -> Bool) -> [a] -> [a]

class Functor f where
  fmap :: (a -> b) -> f a -> f b

zip :: [a] -> [b] -> [(a,b)]
zipWith :: (a -> b -> c) -> [a] -> [b] -> [c]

and :: [Bool] -> Bool
```

``` haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f e []     = e
foldr f e (x:xs) = f x (foldr f e xs)

foldr1 :: (a -> a -> a) -> a -> [a] -> a
foldr1 f [x]    = x
foldr1 f (x:xs) = f x (foldr1 f xs)

foldl :: (b -> a -> b) -> b -> [a] -> b
foldl f e []     = e
foldl f e (x:xs) = foldl f (f e x) xs

scanl :: (b -> a -> b) -> b -> [a] -> [b]
scanl f e = map (foldl f e) . inits

inits :: [a] -> [[a]]
inits []     = [[]]
inits (x:xs) = [] : map (x:) (inits xs)

tails :: [a] -> [[a]]
tails []     = [[]]
tails (x:xs) = (x:xs):tails xs
```



## The Haskell 2010 Report

### Control.Monad

### Data

#### Data.Array
#### Data.Bits
#### Data.Char
#### Data.Complex
#### Data.Int
#### Data.Ix
#### Data.List
#### Data.Maybe
#### Data.Ratio
#### Data.Word

### Foreign

#### Foreign.C
##### Foreign.C.Error
##### Foreign.C.String
##### Foreign.C.Types
##### Foreign.ForeignPtr

#### Foreign.Marshal
##### Foreign.Marshal.Alloc
##### Foreign.Marshal.Array
##### Foreign.Marshal.Error
##### Foreign.Marshal.Utils

#### Foreign.Ptr
#### Foreign.StablePtr
#### Foreign.Storable

### Numeric

### System

#### System.Environment

#### System.Exit

#### System.IO

##### System.IO.Error
