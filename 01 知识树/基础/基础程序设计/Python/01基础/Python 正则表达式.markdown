# TODO
- 正则表达式还是要好好总结下的，因为在查找搜索的时候经常要用到。




# 问题：python re FutureWarning: split() requires a non-empty pattern match
## 问题原因
使用 re.compile(r'\s*') 对英文文档进行分词的时候，报出的warning：
```python
    # 将文本内容切分成 word list
    def text_parse(content):
        import re
        list_of_words = re.split(r'\W*', content)
        return [tok.lower() for tok in list_of_words if len(tok) > 2]
```

## 问题的解决
You are getting this warning because with the \s* pattern you asked to split on substrings of zero or more whitespaces
But... the empty string matches that pattern, because there are zero whitespaces in it!
You could  replace * with +.





# REF
  1. [regular expression split : FutureWarning: split() requires a non-empty pattern match](https://stackoverflow.com/questions/47564710/regular-expression-split-futurewarning-split-requires-a-non-empty-pattern-m)
