---
title: pytest
categories: notes
date: 2020-08-26 22:51:22
tags: pytest
---

[pytest documentation](https://docs.pytest.org/en/5.1.3/getting-started.html#create-your-first-test)


# 装饰器
---

>装饰器parametrize

```
@pytest.mark.parametrize(argnames, argvalues, ids=None):
```
- argnames:用逗号分隔的字符串，表示一个或多个参数名称，或参数字符串的列表/元组。
- argvalues:如果仅指定了一个argname，则argvalues是值的列表。
- 如果指定了N个argname，则argvalues必须是N个元组的列表,
- 其中每个元组元素都为其各自的argname指定一个值。

<!-- more -->
>装饰器skip,跳过测试用例
- 无条件的跳过模块中的所有测试：
```
pytestmark = pytest.mark.skip("all tests still WIP")
```
- 有条件的跳过模块中的所有测试：
```
pytestmark = pytest.mark.skipif(sys.platform == "win32", "tests for linux only")
```
- 当import错误时，跳过模块中的所有测试：
```
pexpect = pytest.importorskip("pexpect")
```
>XFail：标记测试用例是期望失败的
```
@pytest.mark.xfail
```
- strict参数,这个参数会让XPASS的结果在测试报告中变成失败。
```
@pytest.mark.xfail(strict=True) 
def test_function(): ...
```
- 也可以在ini中通过xfail_strict来指定strict参数：
```
[pytest] 
xfail_strict=true
```
- reason参数,如同skipif一样，你可以标记你在某个特定平台上期望测试是失败的。
```
@pytest.mark.xfail(sys.version_info >= (3, 6), reason="Python3.6 api changes") 
def test_function(): ...
```
- raises参数,如果你想更清楚的表达测试为什么失败，你可以用raises参数指定一个或者一组异常
```
@pytest.mark.xfail(raises=RuntimeError) 
def test_function(): ...
```
- run 参数,如果一个测试应该被标记为xfail并且预期是失败的，但是你又暂时不想运行这个测试用例，那么你可 以将run参数设置为False
```
@pytest.mark.xfail(run=False) 
def test_function(): ...
```
- 忽略xfail:在命令行中指定下面的参数,可以强制让xfail和pytest.xfail失效。

---

# 指令
---

查看Pytest收集到的测试用例
```
py.test --collect-only
```
>pytest可选参数
- -q The -q/--quiet flag keeps the output brief in this and following examples.
- --fixtures #shows builtin and custom fixtures
- --markers #list all the markers, including builtin and custom
```
pytest ‐‐runxfail
```

>插件提供了两种命令行方式来运行上一次pytest测试失败的用例：
- –lf, --last-failed 仅重新运行失败的用例
- –ff, --failed-first 先运行上次失败的用例，然后再运行剩余的其他用例
- –nf，–new-first：首先运行新增/修改的用例，然后再运行已有的用例。所有的 用例都会按照修改时间来排序
- –cache-clear参数用来在开始一轮新的测试前清理所有的缓存。（通常并不需要这么做）
- –cache-show：查看缓存内容
- –cache-clear：清理缓存

>逐步调试
- 作为–lf-x的一种替代方法，特别是你预期到你的大部分测试用例都会失败的情况下，使用–sw， -- stepwise 允许你每次运行的时候都修复一个用例。测试集将运行到第一个失败的用例，然后自行停 止，下一次再调用时，测试集将从上次失败的用例继续运行，然后运行到下一次失败的测试用例再停 止。你可以使用–stepwise-skip选项来跳过一个失败的用例，在下一个失败的用例处再停止，如果你 一直搞不定当前的失败的用例且只想暂时忽略它，那么这个选项非常有用。

>插件
- 查看当前环境已激活插件
```
pytest --trace-config
```