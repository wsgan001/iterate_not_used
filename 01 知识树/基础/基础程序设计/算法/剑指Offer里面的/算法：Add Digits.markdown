
## 题目地址：


[https://leetcode.com/problems/add-digits/description/](https://leetcode.com/problems/add-digits/description/)


## 题目内容：


Given a non-negative integer `num`, repeatedly add all its digits until the result has only one digit.
For example:

Given `num = 38`, the process is like: `3 + 8 = 11`, `1 + 1 = 2`. Since `2` has only one digit, return it.

**Follow up:**
Could you do it without any loop/recursion in O(1) runtime?


## 我的想法：

使用一个递归函数，来不断的求生成的数字的位数和

## 我的解法：



```python
class Solution:
    def addDigits(self, num):
        """
        :type num: int
        :rtype: int
        """
        def count_sum(number):
            str_num=str(number)
            if  len(str_num)==1:
                return number
            s=0;
            for item in str_num:
                s+=int(item)
            return count_sum(s)
        return count_sum(num)
```



## 别人的解法：



```python
class Solution:
    def addDigits(self, num):
        """
        :type num: int
        :rtype: int
        """
        if num == 0:
            return 0
        return num % 9 or 9
```



## 别人的想法：





## COMMENT:


为什么可以对9求余数？
