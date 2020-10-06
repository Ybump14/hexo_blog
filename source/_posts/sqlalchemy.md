---
title: sqlalchemy
categories: notes
date: 2020-09-17 22:47:02
tags: sqlalchemy
---

[sqlalchemy1.3官方文档地址](https://docs.sqlalchemy.org/en/13/)

```python
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()
```

```python
class User(Base):
    #__tablename__是必须的(表名)
    __tablename__ = 'users' 

    #数据列,至少一个Column是主键的一部分
    id = Column(Integer, primary_key=True)  
    loginnum = Column(String(255))
    password = Column(String(255))

    #重写__repr__,输出实例化对象信息
    def __repr__(self):
        return "<User(id='%s', loginnum='%s',password='%s')>" % (
            self.id, self.loginnum, self.password)
```

<!-- more -->
创建会话
```python
def sql_connect():
    #创建数据库连接
    #针对不同类型的数据库，create_engine提供了不同的参数格式填写,这里使用的是mysql
    engine = create_engine(
        "mysql://root:root@localhost:3306/test?charset=utf8", encoding='utf-8')
    #定义Session类
    DBSession = sessionmaker(bind=engine)
    #这个定制的Session类将创建绑定到我们数据库的新Session对象
    session = DBSession()
    #针对数据库中不存在的表,发出CREATE TABLE
    Base.metadata.create_all(engine)
    return session
```

添加操作
```python
db = sql_connect()
db_add = User(loginnum="a123",password="123")
db.add(db_add)
db.commit()
db.close()
```

查询操作
```python
db = sql_connect()
db_user = db.query(User).filter().all() #使用all(),返回所有查询结果,数据类型为list
db_user = db.query(User).filter().first() #使用first(),返回查询结果中的第一条数据
print(db_user)

输出结果：
[<User(id='1', loginnum='a123',password='123')>]
```

更新操作
```python
db = sql_connect()
db_user = db.query(User).filter().all()
print(db_user)
db_user[0].password = 456
db_user = db.query(User).filter().all()
print(db_user)
db.commit()
db.close()

输出结果：
[<User(id='1', loginnum='a123',password='123')>]
[<User(id='1', loginnum='a123',password='456')>]
```

删除操作
```python
db = sql_connect()
db = sql_connect()
db_user = db.query(User).filter().first()
print(db_user)
db.delete(db_user)
db.commit()
db_user = db.query(User).filter().all()
print(db_user)

输出结果：
[<User(id='1', loginnum='a123',password='456')>]
[]
若以all()查询,执行delete方法时,直接给整个list会出现报错,必须指定list中的元素,或用first()单独查询一条数据进行删除
```