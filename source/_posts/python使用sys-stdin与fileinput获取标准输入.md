---
title: python使用sys.stdin与fileinput获取标准输入
comments: true
date: 2018-06-08 19:15:14
update: 2018-06-08 19:15:14
tag: ["python", "linux"]
categories: python
top:
---

> Shell脚本可以直接利用管道衔接不同的Linux命令，通过管道可以使用多个简单的命令实现复杂的功能。在Python中也希望可以利用管道。

# stdin
Python的标准库`sys`提供了三个文件描述符:

|标准输入|标准输出|错误输出|
|:-:|:-:|:-:|
|stdin|stdout|stderr|


以下例子是获取标准输入，然后写入到标准输入。
```python read_stdin.py
import sys

for line in sys.stdin:
    sys.stdout.write(line)

```

接下来我们可以在命令行使用该脚本。
```bash
$ cat /etc/passwd | python read_stdin.py
$ python read_stdin.py < /etc/passwd
$ python read_stdin.py -
```

`sys.stdin`为文件描述符，故拥有文件对象的方法，我们可以使用`read()`方法读取标准输入的所有内容，或者使用`readlines()`将标准输入内容读取到一个`list`中。

```python upper.py
import sys

def get_upper_list():
    content_list =  sys.stdin.readlines()
    return [i.upper() for i in content_list]

for line in get_upper_list():
    sys.stdout.write(line)

```
上述脚本中定义了`get_content()`函数，该函数使用`sys.stdin`标准输入的`readlines()`方法读取所有行，以列表类型保存于`content_list`，使用列表解析，调用每个元素(字符串类型)的`upper()`方法，构造一个新的列表并返回。
迭代`get_upper_list()`函数返回的新列表，将每一行写入标准输入。

该脚本运行效果如下：
![](http://p9lal5uqx.bkt.clouddn.com/python使用sys-stdin与fileinput获取标准输入/20180609095401158.png)

利用`sys.stdin`，我们几乎可以不再使用`awk`。将Python与Linux管道结合，可以充分发挥Python语言的文字处理能力。

# fileinput

对于`awk`，它可以同时处理多个文件，在Python中我们可以使用`fileinput`这个标准库来达到同样效果。
`fileinput`比`sys.stdin`更为通用，它可以遍历`sys.argv[1:]`列表(所有命令行参数)中的文件，如果该列表为空(没有提供文件名参数)，则默认读取标准输入的内容。

```python read_from_fileinput.py
import fileinput

for line in fileinput.input():
    print(line, end="")
```
上述脚本直接调用了fileinput模块的input函数，该函数返回一个FileInput实例化的可迭代对象，可以使用for循环遍历取得每一行内容。fileinput既可以从标准输入读取数据，也可以从文件中(一个或多个)读取数据。
```bash
$ python read_from_fileinput.py -  # 从标准输入读取数据
$ cat /etc/passwd | python read_from_fileinput.py  # 通过管道读取passwd数据
$ python read_from_fileinput.py < /etc/passwd  # 通过重定向读取passwd数据
$ python read_from_fileinput.py /etc/passwd  # 直接读取passwd文件数据
$ python read_from_fileinput.py /etc/passwd /etc/hosts  # 直接读取passwd与hosts文件数据

```
fileinput还提供了一些方法让我们知道当前读取的内容属于哪一个文件等便利的方法，常用方法如下：
* `filename`: 当前正在读取文件的文件名 str
* `fileno`: 文件的描述符 int
* `filelineno`: 正在读取的行时当前文件的第几行 int
* `isfirstline`: 正在读取的行是否是当前文件的第一行 bool
* `isstdin`: 正在读的问价是否时从标准输入读取的 bool
* `nextfile`: 关闭当前正在读取的文件，从下一个文件第一行开始 None

我们可以完成一个简单的脚本来展示一下fileinput的各个方法：

```python read_file.py
import fileinput

for line in fileinput.input():
    meta = ["文件名:" + str(fileinput.filename()),
    " 文件描述符:" + str(fileinput.fileno()),
    " 行号:" + str(fileinput.filelineno()),
    " 首行:" + str(fileinput.isfirstline()),
    " 标准输入:" + str(fileinput.isstdin()) + " "]
    meta_ljust = [i.ljust(9) for i in meta]
    print(*meta_ljust, end="")
    print(line, end="")

```

![](http://p9lal5uqx.bkt.clouddn.com/python使用sys-stdin与fileinput获取标准输入/20180609114727921.png)

同时fileinput还提供了钩子函数，可以帮助去自定义文件访问的方式：
fileinput内置了两个函数：
`hook_compressed`: 识别并打开`.gz`和`.bz2`的压缩文件
`hook_encoded`: 以特定的编码格式打开文件

读取一个`.gz`压缩文件，如下所示：
```python read_compressed.py
import fileinput

for line in fileinput.input(openhook=fileinput.hook_compressed):
    print(line.decode('utf-8'), end="")

```
![](http://p9lal5uqx.bkt.clouddn.com/python使用sys-stdin与fileinput获取标准输入/20180610121207368.png)

在中文windows下有读取utf-8,或在linux下读取gbk需要指定对应的编码格式，如下所示：

```python read_encoded.py
import fileinput

for line in fileinput.input(openhook=fileinput.hook_encoded(encoding="gbk")):
    print(line, end="")

```
