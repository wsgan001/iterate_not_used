---
typora-root-url: ..\..\..\..\_images
typora-copy-images-to: ..\..\..\..\_images
---

## # python 位运算

## <<


  * &

  * |

  * ^




### 几个例子：




#### 例子1：




    print(1<<4)
    print(0b1101&0b1011)
    print(0b1101|0b1011)
    print(0b1101^0b1011)


输出：


    16
    9
    15
    6




#### 例子2：




    def is_pow2(n):
        return (n&(n-1)==0)
    
    print(is_pow2(16))
    print(is_pow2(32))
    print(is_pow2(33))


输出：


    True
    True
    False





## Comment：


没有怎么深入理解，看下有没有相关的例子深入讲解即应用的
