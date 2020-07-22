---
title: pytest+allure生成测试报告
categories: notes
date: 2020-07-22 20:19:36
tags: pytest
---

>环境准备

- 安装pytest
```
pip install pytest
```
- 安装har2case
```
pip install har2case
```
- 安装allure
```
pip install allure-pytest
```
<!-- more -->

---

**以下为执行步骤说明和相关指令**

---

执行步骤示例

1.先用抓包软件fiddler或charles将请求抓到，然后导出为har文件，放到data文件夹

2.进入data文件夹，执行命令[``` har2case 文件名.har -2y ```]

3.可以看到在data目录下生成了【文件名.yml】文件

4.进入test_List文件，修改代码为cases, parameters = get_test_data("D:/git/pytest/data/文件名.yml")

---

- 执行脚本
```python 
py.test
```
- 指定–alluredir选项及结果数据保存的目录
```
pytest --alluredir ./result/
```
- 测试数据生成测试报告页面，这一步需要提前安装allure命令行
[allure命令行安装](https://www.cnblogs.com/ella-li/p/11770129.html)

```
allure generate ./result/ -o ./report/ --clean
```



---

**相关错误**

---

>将获取测试数据的函数分离，在执行pytest调用测试主函数时，抛出错误，提示找不到相关模块
```
__________________________________________________________________________________________________ ERROR collecting tests/test_Login.py __________________________________________________________________________________________________
ImportError while importing test module 'E:\110_pytest\tests\test_Login.py'.
Hint: make sure your test modules/packages have valid Python names.
Traceback:
tests\test_Login.py:6: in <module>
    from utils.commlib import get_test_data
E   ModuleNotFoundError: No module named 'utils'
```

此时需要在导入模块前加入这两句,必须写在[from xxx.xxx import xxx]之前,否则无效
```
import os, sys, allure, requests
sys.path.append(os.getcwd())
```

---

**其他说明**

---


需要找到har2case包所谓位置，修改core.py文件
```python
IGNORE_REQUEST_HEADERS中，加入自己需要过滤的头部属性
```

```python
teststep_dict = {
            "name": "",
            "request": {},
            "validate": []
        }

找到以上内容，修改为

teststep_dict = {
            "name": "",
            "request": {},
            "validate": {}
        }
```
>找到方法def _make_validate(self, teststep_dict, entry_json):

```python
以下代码
teststep_dict["validate"].append(
            {"eq": ["status_code", entry_json["response"].get("status")]}
        )

修改为

teststep_dict["validate"]["code"] = entry_json["response"].get("status")
```

```python
以下代码
teststep_dict["validate"].append(
                    {"eq": ["content.{}".format(key), value]}
                )

修改为

teststep_dict["validate"][format(key)] = value

```


