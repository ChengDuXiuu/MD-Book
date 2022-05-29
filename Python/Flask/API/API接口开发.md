# 接口开发

## 初衷

想为小程序搞个界面，但是Python做界面并不是它的强项而且学无所用，因此想到web的方式，兼容跨平台且本人熟悉一些前端开发栈，因此搞个Python的web接口然后配合Vue实现快速开发且更好地交互。



## flask环境准备

1. 安装flask

	```python
	pip install flask
	```

2. hello world

	```python
	from flask import Flask
	
	# 实例化Flask类
	app = Flask(__name__)
	
	
	# 创建接口
	@app.route("/")
	def hello_world():
	    return "hello world flak!!"
	
	
	# 类似于Java中的主启动类
	app.run()
	# app.run(host, port, debug, options)
	
	#Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
	```

	| **host** 要监听的主机名。 默认为127.0.0.1（localhost）。设置为“0.0.0.0”以使服务器在外部可用 |
	| ------------------------------------------------------------ |
	| **port** 默认值为5000                                        |
	| **debug** 默认为false。 如果设置为true，则提供调试信息       |
	| **options** 要转发到底层的Werkzeug服务器。                   |

	![image-20220522002738916](images/image-20220522002738916.png)



## Flask路由规则

```python
# 默认字符串 格式为 <xxx>
@app.route("/get/<param>")
def get_req(param):
    return "Get Param : %s" % param
```

```python
# 指定参数类型 格式为 <[int|float|path|uuid]:xxx>
@app.route("/get/<int:param>")
def get_req(param):
    return "Get Param : %s" % param
```

> 缺省情况下为 字符串，可指定如下格式：
>
> int ：正整数
>
> float ：正浮点数
>
> path ：路径，类似字符串
>
> uuid ：uuid字符串





## HTTP方法

| **GET**以未加密的形式将数据发送到服务器。最常见的方法。      |
| :----------------------------------------------------------- |
| **HEAD**和GET方法相同，但没有响应体。                        |
| **POST**用于将HTML表单数据发送到服务器。POST方法接收的数据不由服务器缓存。 |
| **PUT**用上传的内容替换目标资源的所有当前表示。              |
| **DELETE** 删除由URL给出的目标资源的所有当前表示。           |

```python
from flask import Flask, redirect, url_for, request
@app.route('/login', methods=['POST', 'GET'])
def login():
    if request.method == 'POST':
        user = request.form['nm']
        return user
    else:
        user = request.args.get('nm')
        return user
```

![image-20220522005837556](images/image-20220522005837556.png)







## 重定向

`格式`

```python
Flask.redirect(location, statuscode, response)
```

- **location**参数是应该重定向响应的URL。
- **statuscode**发送到浏览器标头，默认为302。
- **response**参数用于实例化响应。

```python
# 重定向到外网
@app.route('/bili', methods=['POST', 'GET'])
def bilibili():
    return redirect('https://www.bilibili.com/')
  
# 请求转发
@app.route('/login', methods=['POST', 'GET'])
def login():
    if request.method == 'POST' and request.form['username'] == 'admin':
        return redirect(url_for('success'))
    return redirect(url_for('index'))


@app.route("/index")
def index():
    return "欢迎"


@app.route("/success")
def success():
    return "成功"
```



## Request对象

Request对象的重要属性如下所列：

- **Form** - 它是一个字典对象，包含表单参数及其值的键和值对。
- **args** - 解析查询字符串的内容，它是问号（？）之后的URL的一部分。
- **Cookies** - 保存Cookie名称和值的字典对象。
- **files** - 与上传文件有关的数据。
- **method** - 当前请求方法。

```python
from flask import Flask, redirect, url_for, request
@app.route('/login', methods=['POST', 'GET'])
def login():
    if request.method == 'POST':
        user = request.form['nm']
        return user
    else:
        user = request.args.get('nm')
        return user

@app.route('/json', methods=['POST', 'GET'])
def json():
    json_data = request.get_json()
    print('数据 %s' % json_data)      
```





## session

> 用于服务器记录用户信息，防止统一会话下重复登录

```python
from flask import Flask, request, redirect, url_for, jsonify, session

app = Flask(__name__)

# session设置秘钥，否则会报错
app.secret_key = 'abcedf'


# 首页
@app.route('/index', methods=['POST'])
def index():
    return redirect('https://www.bilibili.com/')


# 登录
@app.route('/login', methods=['POST'])
def login():
    info = request.get_json()
    userName = info.get('userName')
    password = info.get('password')

    if not all([userName, password]):
        return "用户名或密码丢失"

    if userName == 'root' and password == '1234':
        session["userName"] = userName
        return redirect(url_for('index'))
    else:
        return jsonify(msg='用户名密码错误')


# 检查登录状态
@app.route('/check_login', methods=['POST'])
def check_login():
    userName = request.get_json().get('userName')
    if session.get('userName') == userName:
        return jsonify(msg='已经登录')
    else:
        return jsonify(msg='请登录')


# 登出
@app.route('/logout', methods=['GET'])
def logout():
    session.clear()
    return jsonify(msg='退出成功')


app.run('localhost', 5000, True)
```





## 数据处理

### 前端发送JSON 后端返回JSON

```python
from flask import Flask, redirect, request, jsonify
@app.route('/json', methods=['POST', 'GET'])
def json():
    json_data = request.get_json()
    print('数据 %s' % json_data)
    name = json_data.get('name')
    age = json_data.get('age')
    if not all([name, age]):
        return '缺少参数'
    print('name : %s. age %d' % (name, age))
    return jsonify(name='张三', age=18, sex='男')
```





# database

## 关系型数据库

Flask并不原生支持数据库，而是通过Python包以及Flask数据库插件。

数据库分为关系型数据库和非关系型数据库，这里我们使用关系型数据库。我们使用Flask-SQLAlchemy插件，它是SQLAlchemy的Flask插件包，基于对象关系映射ORM。SQLAlchemy支持很多数据引擎，包括MySQL、PostgreSQL、SQLite等。SQLAlchemy和Flask-SQLAlchemy的关系类似于jpa和hibernate。通过下面的命令安装Flask-SQLAlchemy插件：



## PostgresSQL + SQLAichemy

```python
import sqlalchemy
import datetime


def connect(user, password, db, host='localhost', port=5432):
    '''Returns a connection and a metadata object'''
    # We connect with the help of the PostgreSQL URL
    url = 'postgresql://{}:{}@{}:{}/{}'
    url = url.format(user, password, host, port, db)

    # The return value of create_engine() is our connection object
    con = sqlalchemy.create_engine(url, client_encoding='utf8')

    # We then bind the connection to MetaData()
    meta = sqlalchemy.MetaData(bind=con)

    return con, meta


con, meta = connect('postgres', '1314', 'mydb', '192.168.31.200')
from sqlalchemy import Table, Column, Integer, String, ForeignKey, TIMESTAMP

slams = Table('slams', meta,
              Column('name', String, primary_key=True),
              Column('country', String)
              )

results = Table('results', meta,
                Column('slam', String, ForeignKey('slams.name')),
                Column('year', Integer),
                Column('result', String)
                )
# Create the above tables
meta.create_all(con)

def find_all_table():
    """获取所有表信息"""
    for table in meta.tables:
        print(table)


def insert_table_single():
    """插入单值"""
    clause = slams.insert().values(name='Wimbledon', country='United Kingdom')
    res1 = con.execute(clause)

    clause = slams.insert().values(name='Roland Garros', country='France')
    res2 = con.execute(clause)
    # 查看插入的key
    print(res1.inserted_primary_key)
    print(res2.inserted_primary_key)


def insert_table_batch():
    """批量插入"""
    victories = [
        {'slam': 'Wimbledon', 'year': 2003, 'result': 'W'},
        {'slam': 'Wimbledon', 'year': 2004, 'result': 'W'},
        {'slam': 'Wimbledon', 'year': 2005, 'result': 'W'}
    ]

    con.execute(meta.tables['results'].insert(), victories)


def select_assign_table():
    """查询指定表"""
    for row in con.execute(results.select()):
        print(row)


def select_assign_table_condition():
    """条件查询"""
    clause = results.select().where(results.c.year == 2005)
    for row in con.execute(clause):
        print(row.year)


def update_assign_table_condition():
    """条件更新"""
    cc = results.update().where(results.c.year == 2005).values(year=20088)
    con.execute(cc)


def select_function():
    c = con.execute('select max(year) as vv from results')
    names = [row[0] for row in c]
    print(list(names))


if __name__ == "__main__":
    # find_all_table()
    # insert_table_single()
    # insert_table_batch()
    # select_assign_table()

    # update_assign_table_condition()
    # select_assign_table()

    select_function()
```



## PostgresSQL + SQLAichemy + ORM

```python
from sqlalchemy import Column, ForeignKey, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
from sqlalchemy import create_engine

Base = declarative_base()


class Address(Base):
    __tablename__ = 'address'
    id = Column(Integer, primary_key=True)
    street_name = Column(String(250))
    street_number = Column(String(250))
    post_code = Column(String(250), nullable=False)
    person_id = Column(Integer, ForeignKey('person.id'))  # 外键，指明地址与用户的关联关系


class Person(Base):
    __tablename__ = 'person'
    id = Column(Integer, primary_key=True)
    name = Column(String(250), nullable=False)
    addressid = relationship(Address, backref='person', uselist=False)  # backref使得address也能获得person对象，uselist是一对一


url = 'postgresql://{}:{}@{}:{}/{}'.format('postgres', '1314', '192.168.31.200', 5432, 'mydb')
engine = create_engine(url)

# Create all tables in the engine. This is equivalent to "Create Table"
Base.metadata.create_all(engine)
```



```python
from sqlalchemy.orm import sessionmaker

from modles import engine


class operateDb:
    def __init__(self):
        self.DBSession = sessionmaker(bind=engine)
        self.session = self.DBSession()

    def add(self, object):
        self.session.add(object)
        self.session.commit()

    def addmany(self, objectlist):
        self.session.add_all(objectlist)
        self.session.commit()

    def filterone(self, object, filter):
        return self.session.query(object).filter(filter).first()

    def update(self, object, filter, updic):
        self.session.query(object).filter(filter).update(updic)
        self.session.commit()

```



```python
from modles import Address, Person
from operatedb import operateDb
from sqlalchemy import text
from sqlalchemy import and_
from sqlalchemy import or_

operateDb = operateDb()

# 添加多个
per1 = Person(name="chen")
per2 = Person(name="xu")
operateDb.addmany([per1, per2])

address1 = Address(street_name="quanshui", street_number="1", post_code="123", person_id=1)
address2 = Address(street_name="quanshui", street_number="1", post_code="123", person_id=2)
operateDb.addmany([address1, address2])

# 查询地址
address = operateDb.filterone(Address, Address.id == 1)
print(address.person.name)

# 多条件查询
filter = and_(Address.id == 1, Address.street_name == 'quanshui')
address = operateDb.filterone(Address, filter=filter)
print(address.person.name)

# 多条件查询与更新
filter = and_(Address.id == 1, Address.street_name == 'quanshui')
address = operateDb.update(Address, filter=(filter), updic={"street_name": "dalian"})

```



## PostgreSQL + Flask-SQLAlchemy

#### 安装

1. 安装驱动

	```python
	pip install psycopg2
	```

	> Mac 安装 此包时报错，先安装brew install postgresql ，如果此过程中报错，缺什么依赖安装什么依赖即可

2. 安装数据库迁移工具

	```python
	pip install Flask-Migrate
	```

	> 这是一个轻量级的数据库迁移工具，可通过帮助我们创建和重新创建数据库，将数据移入和移入数据库以及识别数据库状态来帮助我们以更清晰的方式与数据库交互。

3. 安装 Flask-SQLAlchemy

	```python
	pip install flask-sqlalchemy
	```

	

#### 配置

| 配置选项                         | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| `SQLALCHEMY_DATABASE_URI`        | 连接数据库。示例：`mysql://username:password@host/post/db?charset=utf-8` |
| `SQLALCHEMY_BINDS`               | 一个将会绑定多种数据库的字典。 更多详细信息请看官文 [绑定多种数据库](https://links.jianshu.com/go?to=http%3A%2F%2Fflask-sqlalchemy.pocoo.org%2F2.3%2Fbinds%2F%23binds). |
| `SQLALCHEMY_ECHO`                | 调试设置为true                                               |
| `SQLALCHEMY_POOL_SIZE`           | 数据库池的大小，默认值为5。                                  |
| `SQLALCHEMY_POOL_TIMEOUT`        | 连接超时时间                                                 |
| `SQLALCHEMY_POOL_RECYCLE`        | 自动回收连接的秒数。                                         |
| `SQLALCHEMY_MAX_OVERFLOW`        | 控制在连接池达到最大值后可以创建的连接数。当这些额外的 连接回收到连接池后将会被断开和抛弃。 |
| `SQLALCHEMY_TRACK_MODIFICATIONS` | 如果设置成 True (默认情况)，Flask-SQLAlchemy 将会追踪对象的修改并且发送信号。这需要额外的内存， 如果不必要的可以禁用它。 |

操作数据库需要先创建一个db对象，通常写在`exts.py`文件里。



```jsx
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()
```

flask项目一般将数据库配置写入`configs.py`文件里面，配置在创建引擎前需写好，不要在程序运行时修改配置，如下。



```php
HOST = '127.0.0.1'
PORT = '3306'
DATABASE = 'flask1'
USERNAME = 'root'
PASSWORD = '123456'

DB_URI = "mysql+pymysql://{username}:{password}@{host}:{port}/{db}?charset=utf8".format(username=USERNAME,password=PASSWORD, host=HOST,port=PORT, db=DATABASE)

SQLALCHEMY_DATABASE_URI = DB_URI
SQLALCHEMY_TRACK_MODIFICATIONS = False
SQLALCHEMY_ECHO = True
```

写完数据库配置后需要和app绑定，`app.py`文件里写flask应用的创建和蓝图的注册等等，如下：



```python
from flask import Flask
import configs
from exts import db

app = Flask(__name__)
# 加载配置文件
app.config.from_object(configs)
# db绑定app
db.init_app(app)
```

#### 模型类型

| 数据类型    | 说明                                     |
| ----------- | ---------------------------------------- |
| Integer     | 整型                                     |
| String      | 字符串                                   |
| Text        | 文本                                     |
| DateTime    | 日期                                     |
| Float       | 浮点型                                   |
| Boolean     | 布尔值                                   |
| PickleType  | 存储一个序列化（ Pickle ）后的Python对象 |
| LargeBinary | 巨长度二进制数据                         |



#### 模型

```python
from PostgreSQL import db

class Cars(db.Model):
    """汽车PO"""
    __tablename__ = 'cars'

    id = db.Column(db.Integer, autoincrement=True, primary_key=True)
    name = db.Column(db.String(), nullable=False)
    model = db.Column(db.String(11))
    doors = db.Column(db.Integer())
    # color = db.Column(db.Enum("白色", "黑色", "粉色"))

    def __init__(self, name, model, doors):
        self.name = name
        self.model = model
        self.doors = doors

    def save(self):
        """入库"""
        db.session.add(self)
        db.session.commit()

    def __str__(self):
        return f"<Car {self.name}>"
```



#### 测试

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from Cars import *

app = Flask(__name__)
# instance里的config通常存放api密钥等不想让人知道的配置
# app.config.from_object(Config())
# 调用的是当前根目录下的config文件
# 此时根目录下没有config文件就不需要这句
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:1314@192.168.31.200:5432/mydb'
# app.config.from_pyfile('config.py')
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
# app.config['SQLALCHEMY_DATABASE_URI'] =
db = SQLAlchemy(app)
db.init_app(app)


if __name__ == "__main__":
    #  对表的操作 创建：# 删除：
    db.drop_all()
    db.create_all()
    car_1 = Cars(name='小鹏', model="小鹏model", doors=123)
    car_1.save()
    car_2 = Cars(name='比亚迪', model="比亚迪model", doors=456)
    car_2.save()
    print(car_1, car_2)
```



