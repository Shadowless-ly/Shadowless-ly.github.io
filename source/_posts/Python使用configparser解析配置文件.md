---
title: Python使用configparser解析配置文件
comments: true
date: 2018-06-11 21:31:57
update: 2018-06-11 21:31:57
tag: python
categories: python
top:
---

在linux中，有很多工具拥有自己的配置文件，如：`vim`的配置文件`~/.vimrc`,`pip`的配置文件`~/.pip/pip.conf`,`MySQL`客户端的配置文件`/etc/mysql/my.conf`等等。

# ini配置文件
使用配置文件，我们就无需每次启动设置参数。而且典型的ini配置文件与编程语言无关，可读性强。

ini配置文件格式如下
```ini config.ini
[client]
port = 3306
user = mysql
password = mysql
host = 127.0.0.1

[mysqld]
basedir = /usr
datadir = /var/lib/mysql
tmpdir = /tmp
skip-external-locking

# 注释

```
其中`[client]`和`[mysqld]`为章节(section),章节下面的`port`,`user`,`password`,`host`这些为选项(option)

# configparser解析配置文件

我们可以使用Python的configparser模块来解析配置文件。
首先我们需要将配置文件读取到内存，并创建一个ConfigParser对象。

<!-- more -->

```python
import configparser

p = configparser.ConfigParser(allow_no_value=True)
p.read("config.ini")

```

以下是ConfigPaser的构造方法常用的参数:
`allow_no_value`: 是否允许选项没有值，默认False
`delimiters`： 分隔符，接受元组，默认("=", ":")
`comment_prefixes`: 注释前缀，接受元组，默认("#", ";")

在生成了ConfigParser对象后，我们可以使用其read方法从配置文件读取配置内容，也可是使用readfp方法直接从文件描述符读取。

常用的ConfigParser查询方法：
`sections`: 返回一个包含所有section的列表
`has_sections`: 判断章节是否存在
`items`: 以元组的方式返回所有选项
`options`: 返回一个包含该章节下所有选项的列表
`has_option`: 判断某个选项是否存在
`get`：取得选项的值
`getboolean`：取得选项的值，boolean类型
`getint`：取得选项的值，int类型
`getfloat`: 取得选项的值，float类型

```python parse.py
import configparser

p = configparser.ConfigParser(allow_no_value=True)
print("读取配置文件", p.read("./config.ini"))
print("返回所有章节列表", p.sections())
print("判断章节是否存在", p.has_section("client"))
print("返回一个章节下所有选项的列表", p.options("client"))
print("判断一个选项是否存在", p.has_option("client", "user"))
print("获取选项的值", p.get("client", "host"))
print("获取选项的值，以int类型返回", p.getint("client", "port"))

```

结果如下：
```bash
读取配置文件 ['./config.ini']
返回所有章节列表 ['client', 'mysqld', 'test']
判断章节是否存在 True
返回一个章节下所有选项的列表 ['port', 'user', 'password', 'host']
判断一个选项是否存在 True
获取选项的值 127.0.0.1
获取选项的值，以int类型返回 3306

```

我们还可以修改配置文件：
`remove_section`: 删除一个章节
`add_section`: 添加一个章节
`remove_option`: 删除一个选项
`set`: 添加一个选项
`write`: 将ConfigParser中的数据写入到文件

```python
import sys
import configparser

p = configparser.ConfigParser(allow_no_value=True)
print("读取配置文件", p.read("./config.ini"))
print("删除一个章节", p.remove_section("client"))
print("添加一个章节", p.add_section("mysql"))
print("添加一个选项", p.set("mysql", "host", "localhost"))
print("添加一个选项", p.set("mysql", "port", "3306"))
print("写入到为文件", p.write(sys.stdout))
```

```bash
读取配置文件 ['./config.ini']
删除一个章节 True
添加一个章节 None
添加一个选项 None
添加一个选项 None
[mysqld]
basedir = /usr
datadir = /var/lib/mysql
tmpdir = /tmp
skip-external-locking

[test]
host = localhost
passwd = 123456

[mysql]
host = localhost
port = 3306

写入到为文件 None

```

我们还可以使用字典访问的形式便捷的取值或赋值。

```python
In [1]: import configparser

In [2]: p = configparser.ConfigParser(allow_no_value=True)

In [3]: p.read("config.ini")
Out[3]: ['config.ini']

In [4]: p["client"]["port"]
Out[4]: '3306'

In [5]: p["client"]
Out[5]: <Section: client>

In [6]: p["client"].items()
Out[6]: ItemsView(<Section: client>)

In [7]: p["client"]["host"]
Out[7]: '127.0.0.1'

In [8]: p["client"].get("host", "none")
Out[8]: '127.0.0.1'

In [9]: p["client"].get("host1", "none")
Out[9]: 'none'

In [10]: p["client"]["host"] = "localhost"

In [11]: p["client"].get("host", "none")
Out[11]: 'localhost'

```