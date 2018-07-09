# TODO
- 对于这个Flask 的 Migrate 还是有一些问题的。比如 列的名称的变更，这个就做不到


# ORIGIN
真正的使用了数据库之后，才感觉数据库的维护的确不是那么简单的，就比如数据库的升级，表中的列添加了；数据的迁移，从 sqlite 迁移到 Mysql；数据的备份，将sqlite 或 mysql 的数据进行备份，备份到本地或者备份到 dropbox ；还有数据的防护 等等。


```python
from app import app, db
from flask_script import Manager, Shell
from flask_migrate import Migrate, MigrateCommand  # 载入migrate扩展
from app.models import User# 如果缺少这句，manage.py 可能检测不到 models

manager = Manager(app)
migrate = Migrate(app, db)  # 注册migrate到flask

manager.add_command('db', MigrateCommand)  # 在终端环境下添加一个db命令

if __name__ == '__main__':
    manager.run()
```

需要使用的指令：

- python manager.py db init
- python manager.py db migrate -m "initial migration"
- python manager.py db upgrade


输入python manager.py db init 来创建迁移仓库,
输入python manager.py db migrate -m "initial migration"来创建迁移脚本, 在数据库结构有变动后创建迁移脚本
输入python manager.py db upgrade 来更新数据库




# REF
  1. [Flask学习记录之Flask-Migrate](https://www.cnblogs.com/agmcs/p/4448094.html)
  2. [Flask-SQLAlchemy 和 Flask-Migrate 使用](https://liuliqiang.info/post/flask-sqlalchemy-and-migrate/%0A)
  3. [Flask-Migrate的使用](https://wing324.github.io/2017/02/26/Flask-Migrate%E7%9A%84%E4%BD%BF%E7%94%A8/)
