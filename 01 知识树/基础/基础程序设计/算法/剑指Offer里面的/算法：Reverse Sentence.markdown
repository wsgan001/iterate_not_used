
## 题目：


字符串按单词反转（必须保留所有空格） ’I love china!' 转化为‘china! love I'


## 我的想法：


先分割一下，然后反转列表


## 我的解答：




    s=sentence.strip()
    l=s.split(' ')
    l=l.reverse()
    s=' '.join(l)
    print(s)




## 评论：


这个解法是错的，因为没有考虑到不同数量的空格。


## 正确的解法：




    # 吧字符串里面的每个单词在进行反转 然后整个句子进行反转
    # 如果直接split 分拆的话 原来的空格就没有办法保留了
    # 但凡需要快速索引的 就用dict 然后需要遍历的 就用list
    sentence = " hello, how are you? Fine.   "


    def reverse(str_list, start, end):
        while start < end:
            # 下面这样写 可以快速交换两个元素的值
            str_list[start], str_list[end] = str_list[end], str_list[start]
            start += 1
            end -= 1

    # 字符串不能修改 因此转化为list
    str_list = list(sentence)# 将字符串的每个字母转换为list的每个元素
    i = 0

    while i < len(str_list):
        if str_list[i] != " ":# 代表我遇到了单词的开始
            start = i
            print('start', start)
            end = start + 1
            # 往后读到最后或者没有空格的地方
            while (end < len(str_list)) and str_list[end] != " ":
                end += 1
            # 对这一段进行反转
            reverse(str_list, start, end - 1)
            i = end
        else:
            i += 1

    print(str_list)
    str_list.reverse()
    print(''.join(str_list))




## COMMENT：


考虑问题一定要全面
