# C++11 ISO/IEC 14882

## 1 General

## The C++ programming language

### 2 Lexical conventions
### 3 Basic concepts
### 4 Standard conversions
### 5 Expressions
### 6 Statements
### 7 Declarations
### 8 Declarators
### 9 Classes
### 10 Derived classes
### 11 Member access control
### 12 Special member functions
### 13 Overloading
### 14 Templates
### 15 Exception handling
### 16 Preprocessing directives

## The Standard C++ library

### 17 Library introduction

#### C++ library headers

``` cpp
<algorithm>
<array>
<atomic>
<bitset>
<chrono>
<codecvt>
<complex>
<condition_variable>
<deque>
<exception>
<forward_list>
<fstream>
<functional>
<future>
<initializer_list>
<iomanip>
<ios>
<iosfwd>
<iostream>
<istream>
<iterator>
<limits>
<list>
<locale>
<map>
<memory>
<mutex>
<new>
<numeric>
<ostream>
<queue>
<random>
<ratio>
<regex>
<set>
<sstream>
<stack>
<stdexcept>
<streambuf>
<string>
<strstream>
<system_error>
<thread>
<tuple>
<type_traits>
<typeindex>
<typeinfo>
<unordered_map>
<unordered_set>
<utility>
<valarray>
<vector>
```

#### C++ headers for C library facilities

``` cpp
<cassert>
<ccomplex>
<cctype>
<cerrno>
<cfenv>
<cfloat>
<cinttypes>
<ciso646>
<climits>
<clocale>
<cmath>
<csetjmp>
<csignal>
<cstdalign>
<cstdarg>
<cstdbool>
<cstddef>
<cstdint>
<cstdio>
<cstdlib>
<cstring>
<ctgmath>
<ctime>
<cuchar>
<cwchar>
<cwctype>
```

### 18 Language support library

|#|Subclause|Header(s)|
|:---|:---|:---|
|18.2 |Types| `<cstddef>`|
|18.3 |Implementation properties | `<limits>` <br> `<climits>` <br> `<cfloat>`|
|18.4 |Integer types | `<cstdint>`|
|18.5 |Start and termination | `<cstdlib>`|
|18.6 |Dynamic memory management | `<new>`|
|18.7 |Type identification | `<typeinfo>`|
|18.8 |Exception handling | `<exception>`|
|18.9 |Initializer lists  | `<initializer_list>`|
|18.10 |Other runtime support | `<csignal>` <br> `<csetjmp>` <br> `<cstdalign>` <br> `<cstdarg>` <br> `<cstdbool>` <br> `<cstdlib>` <br> `<ctime>`|

### 19 Diagnostics library

|#|Subclause|Header(s)|
|:---|:---|:---|
|19.2 |Exception classes | `<stdexcept>`|
|19.3 |Assertions | `<cassert>`|
|19.4 |Error numbers | `<cerrno>`|
|19.5 |System error support | `<system_error>`|

### 20 General utilities library

|#|Subclause|Header(s)|
|:---|:---|:---|
|20.2 |Utility components | `<utility>`|
|20.3 |Pairs | `<utility>`|
|20.4 |Tuples | `<tuple>`|
|20.5 |Fixed-size sequences of bits | `<bitset>`|
|20.6 |Memory | `<memory>` <br> `<cstdlib>` <br> `<cstring>`|
|20.7 |Smart pointers | `<memory>`|
|20.8 |Function objects | `<functional>`|
|20.9 |Type traits | `<type_traits>`|
|20.10 |Compile-time rational arithmetic | `<ratio>`|
|20.11 |Time utilities | `<chrono>` <br> `<ctime>`|
|20.12 |Scoped allocators | `<scoped_allocator>`|
|20.13 |Type indexes | `<typeindex>`|


### 21 Strings library

|#|Subclause|Header(s)|
|:---|:---|:---|
|21.2 |Character traits| `<string>`|
|21.3 |String classes| `<string>`|
|21.7 |Null-terminated sequence utilities| `<cctype>` <br> `<cwctype>` <br> `<cstring>` <br> `<cwchar>` <br> `<cstdlib>` <br> `<cuchar>`|


### 22 Localization library

|#|Subclause|Header(s)|
|:---|:---|:---|
|22.3<br>22.4| Locales<br>Standard locale Categories| `<locale>` |
|22.5| Standard code conversion facets| `<codecvt>`|
|22.6| C library locales| `<clocale>`|

### 23 Containers library

|#|Subclause|Header(s)|
|:---|:---|:---|
|23.2| Requirements||
|23.3| Sequence containers| `<array>` <br> `<deque>` <br> `<forward_list>` <br> `<list>` <br> `<vector>`|
|23.4| Associative containers| `<map>` `<set>`|
|23.5| Unordered associative containers| `<unordered_map>` <br> `<unordered_set>`|
|23.6| Container adaptors| `<queue>` <br> `<stack>`|


### 24 Iterators library

|#|Subclause|Header(s)|
|:---|:---|:---|
|24.2| Requirements||
|24.4<br>24.5<br>24.6| Iterator primitives<br>Predefined iterators<br>Stream iterators| `<iterator>`|

### 25 Algorithms library

|#|Subclause|Header(s)|
|:---|:---|:---|
|25.2<br>25.3<br>25.4| Non-modifying sequence operations<br>Mutating sequence operations<br>Sorting and related operations |`<algorithm>`|
|25.5| C library algorithms| |`<cstdlib>`|

### 26 Numerics library

|#|Subclause|Header(s)|
|:---|:---|:---|
|26.2 |Requirements| |
|26.3 |Floating-Point Environment |`<cfenv>`|
|26.4 |Complex Numbers |`<complex>`|
|26.5 |Random number generation |`<random>`|
|26.6 |Numeric arrays |`<valarray>`|
|26.7 |Generalized numeric operations |`<numeric>`|
|26.8 |C library |`<cmath>` <br> `<ctgmath>` <br> `<tgmath.h>` <br> `<cstdlib>`|

### 27 Input/output library

|#|Subclause|Header(s)|
|:---|:---|:---|
|27.2 |Requirements||
|27.3 |Forward declarations |`<iosfwd>`|
|27.4 |Standard iostream objects |`<iostream>`|
|27.5 |Iostreams base classes |`<ios>`|
|27.6 |Stream buffers |`<streambuf>`|
|27.7 |Formatting and manipulators |`<istream>` <br> `<ostream>` <br> `<iomanip>`|
|27.8 |String streams |`<sstream>`|
|27.9 |File streams |`<fstream>` <br> `<cstdio>` <br> `<cinttypes>`|

### 28 Regular expressions library

|#|Subclause|Header(s)|
|:---|:---|:---|
|28.2| Definitions||
|28.3| Requirements||
|28.5| Constants||
|28.6| Exception type||
|28.7| Traits||
|28.8| Regular expression template| `<regex>`|
|28.9| Submatches||
|28.10| Match results||
|28.11| Algorithms||
|28.12| Iterators||
|28.13| Grammar||


### 29 Atomic operations library

|#|Subclause|Header(s)|
|:---|:---|:---|
|29.3| Order and Consistency||
|29.4| Lock-free Property||
|29.5| Atomic Types |`<atomic>`|
|29.6| Operations on Atomic Types||
|29.7| Flag Type and Operations|
|29.8| Fences||

### 30 Thread support library

|#|Subclause|Header(s)|
|:---|:---|:---|
|30.2| Requirements||
|30.3| Threads |`<thread>`|
|30.4| Mutual exclusion |`<mutex>`|
|30.5| Condition variables |`<condition_variable>`|
|30.6| Futures |`<future>`|

## Annex

### A Grammar summary
### B Implementation quantities
### C Compatibility
### D Compatibility features
### E Universal character names for identifier characters
### F Cross references
