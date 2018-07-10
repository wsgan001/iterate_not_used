TODO

* **很多问题，要根据书系统的学习一下。**




# 缘由


gunicorn 的 log 文件总是没有写权限，因此我想通过 chmod 666来修改这个文件的权限的时候，发现，我用 evo 创建的这个文件夹和里面的log文件，但是我即使用 chmod 666 gunicorn_error.log 来修改log文件的权限时，它说：

chmod: changing permissions of ‘gunicorn_error.log’: Operation not permitted

OK，我换成了 sudo chmod 666 gunicorn_error.log 它没有反应，然后 我cd logfiles，然后 ls -l logfiles，它说：

ls: cannot access logfiles/gunicorn_error.log: Permission denied

而且这个时候详细信息都是 问号：

-????????? ? ? ? ? ? gunicorn_error.log

然后 我 su root ，我再 ls 这时候是正常的，然后我 chmod 666 logfiles ，然后再 ls -l logfiles ，没有任何改变，还是 ：

-rw-r--r-- 1 root root 2461 Jun 5 08:05 gunicorn_error.log


# 因此有很多问题


**gunicorn 想写log文件的时候，这个user 是谁？是用root账户运行的root 吗？还是gunicorn ？有gunicorn 这个账户吗？还是supivisor？它运行的gunicorn。还是gunicorn 所在的 evo ？它这个exe是在这个 /home/evo 下面的。还是什么？为什么会没有写这个在 /home/evo 里面的这个文件夹的权限？**

**到底该怎么修改文件和文件夹的权限？使用 root 和 evo 和sudo evo 到底有什么区别？我怎么知道两个账户是不是同一个group的？如果一个文件夹是在 /home/evo 里面的，那么谁有权限修改？为什么会显示问号？为什么我上面改了之后却没有变？**
