# python3 bytes类型相关

# ORIGIN


看教程视频，看到python3新添加了bytes类型。

# TODO

- **bytes到底是什么类型？**
- **为什么要添加这种类型？**
- **什么时候使用？**
- **与别的类型之间的转换有什么需要注意的？**
- **详细的用法是什么？**



# 几个例子

## 例子1：


    # 2 是字节长度
    # to_bytes 就是转换为二进制字符串  
    # 就是显示它在内存中是什么形式的？？
    print((1024).to_bytes(2,byteorder='big'))# 高位在前
    print((1024).to_bytes(2,byteorder='little'))# 低位在前
    print((-1024).to_bytes(2,byteorder='big',signed=True))# 高位在前
    print((-1024).to_bytes(2,byteorder='little',signed=True))# 低位在前
    
    print((1024).to_bytes(4,byteorder='big'))# 高位在前
    print((1024).to_bytes(4,byteorder='little'))# 低位在前
    print((-1024).to_bytes(4,byteorder='big',signed=True))# 高位在前
    print((-1024).to_bytes(4,byteorder='little',signed=True))# 低位在前


输出：


    b'\x04\x00'
    b'\x00\x04'
    b'\xfc\x00'
    b'\x00\xfc'
    b'\x00\x00\x04\x00'
    b'\x00\x04\x00\x00'
    b'\xff\xff\xfc\x00'
    b'\x00\xfc\xff\xff'

## 例子2：




    print((3124).to_bytes(2,byteorder="big"))
    print('%x'%3124)
    print('%d'%0x0c34)


输出：


    b'\x0c4'
    c34
    3124

**注意：之所以第一行输出的是0xc4 是因为4的ASICC码是34，所以当输出为\x0c\x34 的时候，python直接把\x34 打印成4**





# REF

