---
title: 创建一个最小的flask应用
categories: notes
date: 2020-09-01 22:42:15
tags: flask
---

```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello_world():
    return 'Hello, World!'
if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)
    #当host为loclhost时,服务默认启动在本机5000端口,访问地址为127.0.0.1:5000
    #当host为0.0.0.0时,服务器也可以在外部使用
    #debug=Ture启动时,服务器将自动重新加载代码进行代码更改,发生异常时会显示调试器
```

现在在浏览器中打开 http://127.0.0.1:5000/ ，应该可以看到 Hello World! 字样。