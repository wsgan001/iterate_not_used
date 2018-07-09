---
typora-root-url: ..\..\..\..\..\..\..\Pictures\wikipic
---




## 缘由：


在使用dir()的时候经常会看到打印出很多__xx__这种格式的属性和方法，这种到底是做什么用的呢？


## 解答：




一些资料先放在这里：


    'ABC'.__len__()
    
    #我们自己写的类，如果也想用len(myObj)的话，就自己写一个__len__()方法
    class MyObject:
        def __len__(self):
            return 100
    
    obj = MyObject()
    len(obj)
