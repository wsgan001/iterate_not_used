# TODO
- <font color=red>还是要再确认下，这个到底是起到什么作用？</font>



# ORIGIN
- 这个是在看别人的代码的时候看到的，一直不知道为什么要使用 imp.new_module() ，而且这个好像是在 python2.7的代码里用的，python 3 好像是不用的，**确认下。**





# MAIN



imp.new_module(name)

Return a new empty module object called name. This object is not inserted in sys.modules.
Deprecated since version 3.4: Use types.ModuleType instead.

<font color=red>是被替代了吗？ types.ModuleType  是什么？什么时候用到的？</font>





# REF
1. [Python importlib's analogue for imp.new_module()](https://stackoverflow.com/questions/32175693/python-importlibs-analogue-for-imp-new-module)
