这个是要注意的。

# 什么时候要复制表？

sqlite 不支持 删除列，因此一般是创建一个新的表，然后把旧表中的内容复制到新的表中去。

# 这里面有一个坑

如果我们用：

```
create table newTb as select * from oldTb
# 或者：
create table newTb as select id,name from oldTb
```

那么我们就掉坑里了，因为这样的 create 出来的newTb 实际上很多的信息都丢失了，比如与别的表的 key 之间的练习，比如谁是主key，等等：

比如这是我原始的 book table 的信息：

```sql lite
-- Describe BOOK
CREATE TABLE book (
	id INTEGER NOT NULL, 
	name VARCHAR(255) NOT NULL, 
	access INTEGER NOT NULL, 
	status INTEGER NOT NULL, 
	brief TEXT NOT NULL, 
	selected_chapter INTEGER NOT NULL, 
	publish_timestamp DATETIME NOT NULL, 
	updatetime DATETIME NOT NULL, 
	timestamp DATETIME NOT NULL, 
	cover VARCHAR(255) NOT NULL, 
	user_id INTEGER NOT NULL, 
	PRIMARY KEY (id), 
	FOREIGN KEY(user_id) REFERENCES user (id) ON DELETE CASCADE ON UPDATE CASCADE
)
```

然后我运行：

```
create table newTb as select * from book
```

得到的 newTb 的信息：

```sql lite
-- Describe NEWTB
CREATE TABLE newTb(
  id INT,
  name TEXT,
  access INT,
  status INT,
  brief TEXT,
  selected_chapter INT,
  publish_timestamp NUM,
  updatetime NUM,
  timestamp NUM,
  cover TEXT,
  user_id INT
)
```

可见，丢失了很多的信息，基本上不能用。

OK，我暂时的方法就是按照这个 CREAT_TABLE ... 的语句重新建立起来。而不用 create table newTb as select * from book 这种方法。



# REF

- [SQlite在已创建的表中插入,删除一列](https://my.oschina.net/u/2607809/blog/619220) 这个方法是有问题的，生成的table 很多的 key 信息丢失了。

