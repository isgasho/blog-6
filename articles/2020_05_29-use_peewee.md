# 用peewee代替SQLAlchemy

用了几年SQLAlchemy，但是说实话，复杂的很，包括配套的DB Migration工具alembic。想要配置一个自动生成migration也是很复杂。
最近用了几天peewee，发现真的是简单好用。

咋用呢？

## 常见操作

我们来看看常见的操作：

- 定义model(investment.py)

```python
import datetime

from peewee import (
    CharField,
    DecimalField,
    DateTimeField,
)

from .base import BaseModel


class Investment(BaseModel):
    project_name = CharField(help_text="出资项目名")
    user_name = CharField(help_text="出资人")
    money = DecimalField(help_text="资金数量")
    transfer_at = DateTimeField(help_text="转账日期", default=datetime.datetime.now)
    remark = CharField(help_text="备注")
```

具体Field的属性详见：http://docs.peewee-orm.com/en/latest/peewee/models.html#field-types-table

- 增删查改(base.py)

```python
import datetime

from playhouse.db_url import connect
from peewee import (
    Model,
    DateTimeField,
)

from config import config

db = connect(config.DB_URL)


class BaseModel(Model):
    class Meta:
        database = db

    created_at = DateTimeField(default=datetime.datetime.now)
    updated_at = DateTimeField(default=datetime.datetime.now)
    deleted_at = DateTimeField(null=True, default=None)

    @classmethod
    def get_all(cls):
        return cls.select().order_by(cls.id.desc())
```

或者如下例子（来自官网，不可直接运行）：

```python
>>> user = User.get(User.id == 1)
>>> user.delete_instance()  # Returns the number of rows deleted.
1

>>> User.get(User.id == 1)
UserDoesNotExist: instance matching query does not exist:
SQL: SELECT t1."id", t1."username" FROM "user" AS t1 WHERE t1."id" = ?
PARAMS: [1]
>>> query = Tweet.delete().where(Tweet.creation_date < one_year_ago)
>>> query.execute()  # Returns the number of rows deleted.
7
```

## 数据库migration

使用 [peewee_migrate](https://github.com/klen/peewee_migrate) 来操作，但是它本身还不够好用，为了把它和项目结合起来，
我写了一个脚本(migrate.py)：

```python
#!/home/jiajun/.pyenv/shims/python
import sys

from peewee_migrate import Router

from models import db


def gen():
    with db:
        router = Router(db)
        router.create(auto=True)


def run():
    with db:
        router = Router(db)
        router.run()


def new():
    with db:
        router = Router(db)
        router.create()


if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: ./migrate.py gen|run|new")
        sys.exit(-1)

    action = sys.argv[1]
    if action == "run":
        run()
    elif action == "gen":
        gen()
    elif action == "new":
        new()
```

这样就可以通过执行命令来完成对应的操作：

```bash
$ ./migrate.py gen  # 根据model定义自动生成migration操作
$ ./migrate.py run  # 执行migration操作
$ ./migrate.py new  # 创建新的空的migration操作
```

## 数据库连接池

如上，我用了playhouse里的connect，那该咋处理连接池呢？把连接串写成类似 `mysql+pool://username:password@127.0.0.1:3306/db`
就可以了。但是如果你想要配置连接池的一些参数，似乎就不能这样写了，就得自己用 `PooledMySQLDatabase` 了，可以参照文档：

http://docs.peewee-orm.com/en/latest/peewee/playhouse.html#connection-pool

## 和Flask一起用

如上，配置了连接池之后，如果你直接跑，你会发现请求到了一定量之后，会报连接池满了的异常。那咋办呢？答案就是每次使用完
之后，都加上 `db.close`：

```python
# This hook ensures that a connection is opened to handle any queries
# generated by the request.
@app.before_request
def _db_connect():
    db.connect()

# This hook ensures that the connection is closed when we've finished
# processing the request.
@app.teardown_request
def _db_close(exc):
    if not db.is_closed():
        db.close()
```

好了，差不多就是这些，总体来说，peewee比SQLAlchemy要简单太多，以后就是我的御用ORM了。
