---
title: python日志模块logging使用介绍
comments: true
date: 2018-06-17 00:25:58
update: 2018-06-17 00:25:58
tag: python
categories: python
top:
---

在平时编写Python脚本时，需要调试就加`print`打印出来观察，这种操作在写一些简单的脚本时是迅速有效的。但是当进行复杂的应用开发时，这样调试就显得力不从心。所以我们需要使用Python标准库中的`logging`模块，该模块可以定义日志格式，设置过滤信息，选择日志输出位置（文件、标准错误输出、网络等等）。

# 使用基本的logging配置
在一般使用过程中，我们需要按照事件的严重程度将日志划分不同等级，便于我们观察和定位。
logging内置的日志级别为：
```python
import logging

logging.debug("debug message")
logging.info("info message")
logging.warning("warning message")
logging.error("error message")
logging.critical("critical message")
```

日志级别的数字值：

|级别|数字值|
|-|-|
|CRITICAL|50|
|ERROR|40|
|WARNING|30|
|INFO|20|
|DEBUG|10|
|NOTSET|0|


`logging`模块中的还有以下几个概念，`Handler`，`Formater`，`Filter`，`Logger`。

* `Handler`: 日志处理器。处理日志信息的输出位置。
* `Formater`：日志的格式化。设置日志的输出格式。
* `Filter`: 日志过滤器。为日志设置过滤条件。
* `Logger`： 日志记录器。在应用中使用该接口记录日志。


首先我们开始最基本的日志配置：

1. 定义日志等级，在默认情况下，只有日志级别在WARNING及以上的信息才会被处理，所以我们需要设置适合的等级。
2. 设置日志的输出格式
3. 设置日志的输出位置

以上三项设置，我们可以直接调用`logging`模块中的`basicConfig`方法，该方法会设置全局日志处理器root_handler,全局日志记录器root_logger,并依据制定参数设置Formater作为root_handler的格式化器。

<!-- more -->

使用方法：

```python log_basic.py
import logging


logging.basicConfig(level=logging.INFO,
                    format="%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s",
                    datefmt="%a, %d %b %Y %H:%M:%S",
                    filename='parser_result.log',
                    filemode='w'
                            )

logging.info("info message")
logging.debug("debug message")
logging.warning("warning message")
logging.error("error message")
logging.critical("critical message")

```

按照上述参数我们可以在parser_result.log文件中获得以下内容：

```bash
Sun, 17 Jun 2018 01:07:17 log_basic.py[line:10] INFO info message
Sun, 17 Jun 2018 01:07:17 log_basic.py[line:12] WARNING warning message
Sun, 17 Jun 2018 01:07:17 log_basic.py[line:13] ERROR error message
Sun, 17 Jun 2018 01:07:17 log_basic.py[line:14] CRITICAL critical message
```

如果需要将日志打印在终端上，我们可以修改上述例子:

```python log_basic.py
import logging
import sys

logging.basicConfig(level=logging.INFO,
                    format="%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s",
                    datefmt="%a, %d %b %Y %H:%M:%S",
                    stream=sys.stdout
                            )

logging.info("info message")
logging.debug("debug message")
logging.warning("warning message")
logging.error("error message")
logging.critical("critical message")
```
Basic的参数介绍如下
```bash basicConfig参数
filename: 指定日志文件名;
filemode: 和file函数意义相同，制定日志文件的打开模式，'w' or 'a';
format: 指定输出的格式和内容.
    %(levelno)s: 日志级别数值
    %(levelname)s:日志级别名称
    %(pathname)s:当前程序路径，sys.argv[0]
    %(filename)s:当前执行程序名
    %(funcName)s:日志的当前函数
    %(lineno)s:当前行号
    %(asctime)s:日志时间
    %(thread)d:线程ID
    %(threadName)s:线程名称
    %(process)d:进程ID
    %(message)s:日志信息
datafmp: 指定时间格式，同time.strftime();
level: 设置日志级别，默认logging.WARNNING;
stream: 指定将日志的输出流，可以sys.stderr,sys.stdout或文件，默认sys.stderr,当filename也指定，stream被忽略；
```

基本的设置使用logging.basic即可完成，后面介绍由Handler，Formater，Filter，Logger这些更细致的配置。

# 日志记录器Logger的使用

Logger为logging模块中的日志记录器，主要执行三项工作：

1. 为程序提供日志记录的接口
2. 判断日志所处级别，并判断是否需要过滤
3. 根据日志级别将该条日志分别发给不同Handler

常用的方法为：

1. `Logger.setLevel()`: 设置日志等级
2. `Logger.addHandler()`: 添加一个`Handler`
3. `Logger.removeHandler()`: 删除一个`Handler`
4. `Logger.addFilter()`: 添加一个过滤器

使用`Logger.getLogger()`可以获取一个`Logger`，如果未提供参数则获得`root Logger`。
`logging.getLogger("APP")`获得一个名为APP的日志记录器
`logging.getLogger("APP.run")`获得名为APP.run的记录器，该记录器为APP的子记录器

示例代码如下：
```python logging_logger.py
import logging
import sys

def app():
    app_logger = logging.getLogger("APP")
    app_logger.setLevel(logging.INFO)
    app_formater = logging.Formatter("%(name)s - %(asctime)s - %(filename)s: %(message)s")
    app_handler = logging.StreamHandler(sys.stdout)
    app_handler.setFormatter(app_formater)
    app_logger.addHandler(app_handler)

    app_logger.info("APP config ...")


def run():
    run_logger = logging.getLogger("APP.run")
    run_logger.setLevel(logging.INFO)
    run_formater = logging.Formatter("%(name)s - %(asctime)s - %(filename)s: %(message)s")
    run_handler = logging.StreamHandler()
    run_handler.setFormatter(run_formater)
    run_logger.addHandler(run_handler)
    
    run_logger.info("running now!")
    

def main():
    app()
    run()

if __name__ == '__main__':
    main()
```

执行代码输出如下：

```bash
shadowless@shadowless-PC:$ python logging_logger.py
APP - 2018-06-17 22:25:11,541 - logging_logger.py: APP config ...
APP.run - 2018-06-17 22:25:11,542 - logging_logger.py: running now!
APP.run - 2018-06-17 22:25:11,542 - logging_logger.py: running now!
```

观察输出结果，为何会有两条`unning now`的打印，这是因为默认情况下，一个`logger`名为`propagate`属性，当该属性值为`True`则该`logger`的输出会朝着上一级传播，最终都会交给`root Logger`。
有如下解决方案：
1. 在创建logger时将`propagate`属性置为`False`。
2. 将APP.run的Handler移除，全部日志都交给APP Logger的处理器去输出。

一般情况下，为了使程序日志记录有调理，我们也可以将root Logger的日志等级设置成最高，或者移除其所有日志处理器。


# 使用不同的Handler来处理日志的输出

logging模块中内置的Handler有以下几种：

|名称|作用|
|-|-|
|StreamHandler|日志输出到流，可以是sys.stderr或sys.stdout或文件|
|FileHandler|日志输出到文件
|BaseRotatingHandler|基本的日志回滚方式
|RotatingHandler|日志回滚方式，支持日志文件最大数量和日志文件回滚
|TimeRotatingHandler|日志回滚方式，在一定时间区域回滚日志文件
|SocketHandler|远程输出到TCP/IP sockets
|DatagramHandler|远程输出日志到UDP socckets
|SMTPHandler|远程输出日志到邮件地址
|SysLogHandler|日志输出到syslog
|NTEventLogHandler|远程输出日志到Windows NT/2000/XP的事件日志
|MemoryHandler|日志输出到内存中的指定buffer
|HTTPHandler|通过"get","post"远程输出到HTTP服务器|

## StreamHandler使用

StreamHandler可以将日志输出到流。如标准输出流，标准错误输出流，文件等。
使用方法可以参考如下例子：

```python logging_stream.py
import logging
import sys

console_logger = logging.getLogger()
console_logger.setLevel(level=logging.INFO)
formatter = logging.Formatter("%(pathname)s - %(asctime)s - %(name)s - %(levelname)s - %(message)s")
console_handler = logging.StreamHandler(sys.stderr)
console_handler.setFormatter(formatter)

console_logger.info("输出到命令行")

```

## FileHandler使用

FileHandler可以将日志输出到文件。
```python logging_file.py
import logging

file_logger = logging.getLogger()
file_logger.setLevel(level=logging.INFO)
formatter = logging.Formatter("%(pathname)s - %(asctime)s - %(name)s - %(levelname)s - %(message)s")
file_handler = logging.FileHanlder("logging_file.log")
file_handler.setFormatter(formatter)

file_logger.info("输出到文件")

```

## RotatingFileHandler
```python logging_rotatingfile.py
import logging

file_logger = logging.getLogger()
file_logger.setLevel(level=logging.INFO)
formatter = logging.Formatter("%(pathname)s - %(asctime)s - %(name)s - %(levelname)s - %(message)s")
file_handler = logging.handlers.RotatingFileHandler("log/logging_file.log", maxBytes=1*1024, backupCount=3)
file_handler.setFormatter(formatter)

file_logger.info("输出到文件,当日志达到1KB时将日志备份，总备份个数为3个，logging_file.log中始终为最新的日志")

```

# 使用Filter过滤器

{% quote python logging模块使用教程 https://www.jianshu.com/p/feb86c06c4f4 好吃的野菜 %}
Handlers和Loggers可以使用Filters来完成比级别更复杂的过滤。Filter基类只允许特定Logger层次以下的事件。例如用‘A.B’初始化的Filter允许Logger ‘A.B’, ‘A.B.C’, ‘A.B.C.D’, ‘A.B.D’等记录的事件，logger‘A.BB’, ‘B.A.B’ 等就不行。 如果用空字符串来初始化，所有的事件都接受。
创建方法:  `filter = logging.Filter(name='')`

概念总结：
熟悉了这些概念之后，有另外一个比较重要的事情必须清楚，即Logger是一个树形层级结构;
Logger可以包含一个或多个Handler和Filter，即Logger与Handler或Fitler是一对多的关系;
一个Logger实例可以新增多个Handler，一个Handler可以新增多个格式化器或多个过滤器，而且日志级别将会继承。


![](http://p9lal5uqx.bkt.clouddn.com/python日志模块logging使用介绍/20180617111350472.png)

{% endquote %}

示例，使用filter控制日志输出到log文件与控制台
```python logging_filter.py
import sys
import logging


class ContextFilter(logging.Filter):
    """
    这是一个控制日志记录的过滤器。
    """
    def filter(self, record):
        try:
            filter_key = record.TASK
        except AttributeError:
            return False

        if filter_key == "logToConsole":
            return True
        else:
            return False


if __name__ == '__main__':
    # 创建日志对象
    logger = logging.getLogger(__name__)
    logger.setLevel(logging.DEBUG)

    # 创建日志处理器，记录日志到文件
    log_path = "./log.log"
    file_handler = logging.FileHandler(log_path)
    file_handler.setLevel(logging.INFO)
    file_fmt = "%(asctime)-15s %(levelname)s [%(filename)s %(lineno)d] %(message)s"
    file_formatter = logging.Formatter(file_fmt)
    file_handler.setFormatter(file_formatter)
    logger.addHandler(file_handler)

    # 添加日志处理器，输出日志到控制台
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setLevel(logging.WARN)

    console_fmt = '%(asctime)-15s [%(TASK)s] %(message)s'
    console_formatter = logging.Formatter(console_fmt)
    console_handler.setFormatter(console_formatter)

    console_filter = ContextFilter()
    console_handler.addFilter(console_filter)

    logger.addHandler(console_handler)

    filter_dict = {'TASK': 'logToConsole'}

    # 记录日志
    logger.debug('debug message')
    logger.info('info message')
    logger.warning('warn message')
    logger.error('error message1', extra=filter_dict)
    logger.error('error message2')
```

# logging模块工作流程

![](http://p9lal5uqx.bkt.clouddn.com/python日志模块logging使用介绍/20180617112412833.png)

1. 判断日志的等级是否大于Logger对象的等级，如果大于，则往下执行，否则，流程结束。
2. 产生日志。第一步，判断是否有异常，如果有，则添加异常信息。第二步，处理日志记录方法(如debug，info等)中的占位符，即一般的字符串格式化处理。
3. 使用注册到Logger对象中的Filters进行过滤。如果有多个过滤器，则依次过滤；只要有一个过滤器返回假，则过滤结束，且该日志信息将丢弃，不再处理，而处理流程也至此结束。否则，处理流程往下执行。
4. 在当前Logger对象中查找Handlers，如果找不到任何Handler，则往上到该Logger对象的父Logger中查找；如果找到一个或多个Handler，则依次用Handler来处理日志信息。但在每个Handler处理日志信息过程中，会首先判断日志信息的等级是否大于该Handler的等级，如果大于，则往下执行(由Logger对象进入Handler对象中)，否则，处理流程结束。
5. 执行Handler对象中的filter方法，该方法会依次执行注册到该Handler对象中的Filter。如果有一个Filter判断该日志信息为假，则此后的所有Filter都不再执行，而直接将该日志信息丢弃，处理流程结束。
6. 使用Formatter类格式化最终的输出结果。 注：Formatter同上述第2步的字符串格式化不同，它会添加额外的信息，比如日志产生的时间，产生日志的源代码所在的源文件的路径等等。
7. 真正地输出日志信息(到网络，文件，终端，邮件等)。至于输出到哪个目的地，由Handler的种类来决定。


# logging的配置

logging可以采用一下几种方式进行配置：
1. 显式创建记录器Logger、处理器Handler和格式化器Formatter，并进行相关设置；
2. 通过简单方式进行配置，使用basicConfig()函数直接进行配置；
3. 通过配置文件进行配置，使用fileConfig()函数读取配置文件；
4. 通过配置字典进行配置，使用dictConfig()函数读取配置信息；
5. 通过网络进行配置，使用listen()函数进行网络配置。

> 参考官方文档 [python 3.52中文文档](https://yiyibooks.cn/xx/python_352/howto/logging.html#logging-advanced-tutorial)

## 显式创建
```python
import logging

# create logger
logger = logging.getLogger('simple_example')
logger.setLevel(logging.DEBUG)

# create console handler and set level to debug
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)

# create formatter
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add formatter to ch
ch.setFormatter(formatter)

# add ch to logger
logger.addHandler(ch)

# 'application' code
logger.debug('debug message')
logger.info('info message')
logger.warn('warn message')
logger.error('error message')
logger.critical('critical message')
```
输出如下
```bash
$ python simple_logging_module.py
2005-03-19 15:10:26,618 - simple_example - DEBUG - debug message
2005-03-19 15:10:26,620 - simple_example - INFO - info message
2005-03-19 15:10:26,695 - simple_example - WARNING - warn message
2005-03-19 15:10:26,697 - simple_example - ERROR - error message
2005-03-19 15:10:26,773 - simple_example - CRITICAL - critical message
```

## 使用ini配置文件设置

```python
import logging
import logging.config

logging.config.fileConfig('logging.conf')

# create logger
logger = logging.getLogger('simpleExample')

# 'application' code
logger.debug('debug message')
logger.info('info message')
logger.warn('warn message')
logger.error('error message')
logger.critical('critical message')
```

配置文件如下：

```ini
[loggers]
keys=root,simpleExample

[handlers]
keys=consoleHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_simpleExample]
level=DEBUG
handlers=consoleHandler
qualname=simpleExample
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=simpleFormatter
args=(sys.stdout,)

[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
datefmt=
```

输出如下：

```bash
$ python simple_logging_config.py
2005-03-19 15:38:55,977 - simpleExample - DEBUG - debug message
2005-03-19 15:38:55,979 - simpleExample - INFO - info message
2005-03-19 15:38:56,054 - simpleExample - WARNING - warn message
2005-03-19 15:38:56,055 - simpleExample - ERROR - error message
2005-03-19 15:38:56,130 - simpleExample - CRITICAL - critical message
```

{% note warning %}
fileConfig()函数有个参数disable_existing_loggers默认为True，主要是为了向后兼容。它会使fileConfig()调用之前的所有记录器被禁用，除非这些记录器或者它们的祖先显式的出现在配置之中；这也许是／不是你需要的。请参考文档以得到更多的信息，如果需要，请指定False。
传递给dictConfig()的字典可以有键disable_existing_loggers，其值为布尔类型，如果没有显式的指明，其默认值为True。这会导致上述的记录器禁用行为，也许不是你希望的，这种情况下将其值设为False。
{% endnote %}

# 使用Dict，Json，YAML

> 在Python 3.2中，引入了一种新的配置日志记录的方法，使用字典来保存配置信息。它提供的功能是上述的基于文件的配置的功能的超集，推荐在新的应用和部署中使用。因为用Python的字典来保存配置信息，而你可以用不同的方法来产生字典，所以你有更多的配置选择。例如你可以使用JSON格式的配置文件，或者使用YAML格式的文件来产生配置字典。或者你可以用Python代码来构造字典，从socket中接受它的pickle形式（Python一种序列化机制），或者任何对你的应用有意义的方式来构造字典。

使用YAML格式：

```YAML
version: 1
formatters:
  simple:
    format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
handlers:
  console:
    class: logging.StreamHandler
    level: DEBUG
    formatter: simple
    stream: ext://sys.stdout
loggers:
  simpleExample:
    level: DEBUG
    handlers: [console]
    propagate: no
root:
  level: DEBUG
  handlers: [console]
```