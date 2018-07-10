# todo
- 以后对os模块进行补充，目前没怎么使用到。
- 记得之前看到过一种使打印出来的字符串变成一种更容易看的形式的方法，之前没有记录，如果再看见，补充下。



# ORIGIN
实际上我是觉得机器学习中用到os模块是比较少的。。基本就是再对文件进行处理的时候会用到（[file directory](http://106.15.37.116/2018/03/21/python-file-directory/)），除此之外，就没有怎么用到过了。但是这次看到了，因此记一下，以后什么时候用到的时候我再进行补充，并补充下是在什么场景下用到的。





代码如下：

```python
import os
print(os.name)
print(os.environ)
```


输出：

```text
nt
environ({'ALLUSERSPROFILE': 'C:\\ProgramData', 'APPDATA': 'C:\\Users\\evo\\AppData\\Roaming', 'COMMONPROGRAMFILES': 'C:\\Program Files\\Common Files', 'COMMONPROGRAMFILES(X86)': 'C:\\Program Files (x86)\\Common Files', 'COMMONPROGRAMW6432': 'C:\\Program Files\\Common Files', 'COMPUTERNAME': 'EVO-LI', 'COMSPEC': 'C:\\Windows\\system32\\cmd.exe', 'FPS_BROWSER_APP_PROFILE_STRING': 'Internet Explorer', 'FPS_BROWSER_USER_PROFILE_STRING': 'Default', 'HOMEDRIVE': 'C:', 'HOMEPATH': '\\Users\\evo', 'LOCALAPPDATA': 'C:\\Users\\evo\\AppData\\Local', 'LOGONSERVER': '\\\\EVO-LI', 'NUMBER_OF_PROCESSORS': '4', 'ONEDRIVE': 'C:\\Users\\evo\\OneDrive', 'OPENCVBUILD': 'E:\\14.Develop\\OpenCV\\opencv\\build', 'OS': 'Windows_NT', 'PATH': 'E:\\11.ProgramFiles\\Anaconda3\\Library\\bin;E:\\17.Start;C:\\Windows\\system32;C:\\Windows;C:\\Windows\\System32\\Wbem;C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\;C:\\Program Files (x86)\\Windows Kits\\8.1\\Windows Performance Toolkit\\;C:\\Program Files\\Microsoft SQL Server\\110\\Tools\\Binn\\;C:\\Program Files\\Intel\\WiFi\\bin\\;C:\\Program Files\\Common Files\\Intel\\WirelessCommon\\;E:\\14.Develop\\Graphviz2.38\\bin;E:\\14.Develop\\Graphviz2.38\\bin\\dot.exe;E:\\11.ProgramFiles\\Anaconda3;E:\\11.ProgramFiles\\Anaconda3\\Scripts;E:\\11.ProgramFiles\\Anaconda3\\Library\\bin;C:\\Users\\evo\\AppData\\Local\\Microsoft\\WindowsApps;E:\\11.ProgramFiles\\Microsoft VS Code\\bin;C:\\Program Files\\Intel\\WiFi\\bin\\;C:\\Program Files\\Common Files\\Intel\\WirelessCommon\\;E:\\14.Develop\\Graphviz2.38\\bin', 'PATHEXT': '.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC', 'PROCESSOR_ARCHITECTURE': 'AMD64', 'PROCESSOR_IDENTIFIER': 'Intel64 Family 6 Model 78 Stepping 3, GenuineIntel', 'PROCESSOR_LEVEL': '6', 'PROCESSOR_REVISION': '4e03', 'PROGRAMDATA': 'C:\\ProgramData', 'PROGRAMFILES': 'C:\\Program Files', 'PROGRAMFILES(X86)': 'C:\\Program Files (x86)', 'PROGRAMW6432': 'C:\\Program Files', 'PSMODULEPATH': 'C:\\Program Files\\WindowsPowerShell\\Modules;C:\\Windows\\system32\\WindowsPowerShell\\v1.0\\Modules', 'PUBLIC': 'C:\\Users\\Public', 'PYCHARM_HOSTED': '1', 'PYTHONIOENCODING': 'UTF-8', 'PYTHONPATH': 'E:\\01.Learn\\01.Python\\01.PythonBasic', 'PYTHONUNBUFFERED': '1', 'SESSIONNAME': 'Console', 'SYSTEMDRIVE': 'C:', 'SYSTEMROOT': 'C:\\Windows', 'TEMP': 'C:\\Users\\evo\\AppData\\Local\\Temp', 'TMP': 'C:\\Users\\evo\\AppData\\Local\\Temp', 'USERDOMAIN': 'EVO-LI', 'USERDOMAIN_ROAMINGPROFILE': 'EVO-LI', 'USERNAME': 'evo', 'USERPROFILE': 'C:\\Users\\evo', 'VS120COMNTOOLS': 'E:\\11.ProgramFiles\\VS\\Common7\\Tools\\', 'WINDIR': 'C:\\Windows'})
```
