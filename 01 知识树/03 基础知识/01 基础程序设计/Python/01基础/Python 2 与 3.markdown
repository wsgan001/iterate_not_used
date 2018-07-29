TODO

- **有些类库在 2 和 3 中已经变更了，也要总结下的，比如urllib requrest 什么的。**





# python 2 与 python 3 的区别


​	
  * print函数：3.x必须加上()
  * Unicode：3.x 默认使用unicode编码
  * 除法运算：3.x中整数相除也能得到浮点数的结果
  * 异常：3.x中只能抛出集成字BaseException的异常
  * xrange：3.x版本中取消了xrange，range与xrange一样位实现为惰性求值。因此range也变得很高效    2.7版本里面效率要求高的时候用xrange
  * 二/八进制：**3.x版本必须强制写成0b1011和0o7236**
  * 不等式：3.x取消了'<>'  只有'!='
  * ”“表达式：3.x必须使用repr函数
  * 多个模块改名：Queue->queue,repr->reprlib...
  * 3.x 取消了long，统一为int
  * 新增bytes类型，可与string相互转换  **bytes类型？**
  * dict的keys/items/values方法返回迭代器，iterkeys函数被废弃，has_key被in取代。

# 对于中文的支持





# Python3 已经是大趋势了

本书使用的是Python3，Python2将会在2020年停止维护，所以整个社群转向Python3已经是大趋势了。

而且Python3不用担心编码问题，对于中文使用环境的我们来说，非常友好。







# REF












