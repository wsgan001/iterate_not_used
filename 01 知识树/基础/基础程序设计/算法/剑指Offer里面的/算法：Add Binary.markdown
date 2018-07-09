
## 题目地址：


[https://leetcode.com/problems/add-binary/description/](https://leetcode.com/problems/add-binary/description/)


## 题目描述：


Given two binary strings, return their sum (also a binary string).

For example,
a = `"11"`
b = `"1"`
Return `"100"`.


## 我的思路：


将二进制字符串转化为int格式，然后计算和，然后再转化为二进制字符串


## 我的实现：



```python
class Solution:
    def addBinary(self, a, b):
        """
        :type a: str
        :type b: str
        :rtype: str
        """
        a_d=int(a,2)
        b_d=int(b,2)
        s=a_d+b_d
        return bin(s)[2:]
```

Runtime: **44 ms**


## 别人的实现：



```python
class Solution:
    def addBinary(self, a, b):
        """
        :type a: str
        :type b: str
        :rtype: str
        """
        return bin(int(a, 2)+int(b, 2))[2:]
```



#### 59 ms




## COMMENT：


为什么写在不同的式子里比写在同一个式子里的时间还要长呢？这个时间是什么？是经过这些检测的时间吗？里面有什么原因吗？
