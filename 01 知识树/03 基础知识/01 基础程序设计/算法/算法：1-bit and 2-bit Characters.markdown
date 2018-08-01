

## 题目地址：


https://leetcode.com/problems/1-bit-and-2-bit-characters/description/


## Description:


We have two special characters. The first character can be represented by one bit `0`. The second character can be represented by two bits (`10` or `11`).

Now given a string represented by several bits. Return whether the last character must be a one-bit character or not. The given string will always end with a zero.

**Example 1:**


    Input:
    bits = [1, 0, 0]
    Output: True
    Explanation:
    The only way to decode it is two-bit character and one-bit character. So the last character is one-bit character.


**Example 2:**


    Input:
    bits = [1, 1, 1, 0]
    Output: False
    Explanation:
    The only way to decode it is two-bit character and two-bit character. So the last character is NOT one-bit character.


**Note:**




  * `1 <= len(bits) <= 1000`.


  * `bits[i]` is always `0` or `1`.




## 我的想法：


创造一个递归函数，将各种可能性进行遍历，然后将所有的无论是可拆解还是不可拆解的结果存放到一个列表中，如果有True的结果，说明是存在可拆解的结果。


## 我的解法：




    class Solution:
        def isOneBitCharacter(self, bits):
            """
            :type bits: List[int]
            :rtype: bool
            """
            if len(bits)==1:
                return True
            bits_t=bits[:(len(bits)-1)]

            result_list=[]
            def try_to_split(bit_list):
                if len(bit_list)==1 :
                    if bit_list[0]==1:
                        result_list.append(False)
                        return
                    else:
                        result_list.append(True)
                        return
                elif len(bit_list)==2 and bit_list[0]==1:
                        result_list.append(True)
                        return
                else:
                    if bit_list[0]==0:
                        return try_to_split(bit_list[1:])
                    else:
                        return try_to_split(bit_list[2:])

            try_to_split(bits_t)
            if True in result_list:
                return True
            else:
                return False


Runtime: **80 ms**


## 别人的解法：




    class Solution:
        def isOneBitCharacter(self, bits):
            """
            :type bits: List[int]
            :rtype: bool
            """
            if len(bits) == 1:
                return True

            s = 0
            i = len(bits) - 2

            while bits[i] == 1 and i >= 0:
                s += 1
                i -= 1
            return s%2 == 0




#### 49 ms




## 别人的解法的思路：


除去最后一位，然后从后往前计算连续的1的个数，如果是偶数个1，那么就可拆解，如果是奇数个1，那么就不可拆解。嗯比如从0开始：

0个1，也就说结尾是00，那么是可拆解的，无论之前是什么

1个1，也就说结尾是010，那么是不可拆解的，因为10肯定要连在一起

2个1，也就是说结尾是0110，那么是可拆解的，因为第一个0的前面无论是1还是0都是可以被消掉的，然后11消掉，然后就是0

3个1，也就是说01110，分析依次如上。


## COMMENT：


但是我还是没明白怎没想到的这个解法的？
