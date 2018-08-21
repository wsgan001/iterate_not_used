

```


import time
from datetime import datetime
import os

def modify_filename(path):
	for p in os.listdir(path):
		p = os.path.join(path, p)
		if os.path.isdir(p):
			modify_filename(p)
		else:
			filemt= time.localtime(os.stat(p).st_mtime)
			fileTime= time.strftime("%Y-%m-%d %H:%M:%S",filemt)

			with open(p, 'r+',encoding= 'utf-8') as f:
				content = f.read()
				f.seek(0, 0)
				f.write('---\n'
					+'title: '+os.path.splitext(os.path.basename(p))[0]+'\n'
					+'toc: true\n'
					+'date: '+fileTime+'\n'
					+'---\n'
					+content)


modify_filename("C://Users//evo//Desktop//empty//新建文件夹")

```
