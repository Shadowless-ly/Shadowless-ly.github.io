---
title: Java的字符串格式化
comments: true
date: 2018-06-10 16:54:42
update: 2018-06-10 16:55:00
tag: ["java"]
categories: java
top:
---

整理一下Java的字符串格式化，方便日后查用。

# Java格式化

在Java中，数字和日期的格式化并没有结合到输出、输入功能上，因为通常显示给用户的是GUI或WEB页面，显然只绑定在命令行输出上是不能满足需求的。
格式化的功能是由`java.util.Formatter`这个类来实现的。为了方便，我们不需要直接调用这个class上的方法，在`Java 5.0`以后的版本，该功能已经加入到输入输出类和`String`上了，我们直接调用`String.format()`这个静态方法传入值与格式即可。

## format()方法基本介绍

`java.util.Formatter`类的`format`方法有两个重载版本: `format(String format, Object ... args)`和`format(Locale l, String format, Object ... args)`，两者区别为前者使用本地语言环境，后者指定语言环境。

Formatter的用法：
```java
%[argument number$][flags][width][.precision]type

argument number: 可选，是一个十进制整数，用于表明参数在参数列表中的位置。第一个参数由 "1$" 引用，第二个参数由 "2$" 引用，依此类推。
flags: 可选，特定类型的选项，例如数字要加逗号或正负号
width: 可选，最小字符数，可以超过，不足则补齐
precision:可选，精确度
type:必须，类型标识
```

Java的格式化主要分为两大类，一类为常规类型、字符类型和数值类型，另一类为日期和时间类型。我们按照这两大类来整理。

<!-- more -->

### 常规类型格式化

#### type:类型标识
```java
System.out.println(String.format("My name is %s", "Tom"));
```
`%`为占位符，`s`为转换符，转换类型需要与参数相兼容。

```java
import java.util.Formattable;
import java.util.Formatter;

public class TestFormat {

    public static void main(String [] args){
        formatConversion();
        }

    private static void formatConversion(){
        System.out.println(String.format("'b':将参数格式化为boolean类型输出，'B'的效果相同,但结果中字母为大写。%b",false));
        System.out.println(String.format("'h':将参数格式化为散列输出，原理：Integer.toHexString(arg.hashCode())，'H'的效果相同,但结果中字母为大写。%h","ABC"));
        System.out.println(String.format("'s':将参数格式化为字符串输出，如果参数实现了 Formattable接口，则调用 formatTo方法。'S'的效果相同。%s",16));
        System.out.println(String.format("FormatImpl类实现了Formattable接口：%s",new FormatImpl()));
        System.out.println(String.format("'c':将参数格式化为Unicode字符，'C'的效果相同。%c",'A'));
        System.out.println(String.format("'d':将参数格式化为十进制整数。%d",11));
        System.out.println(String.format("'o':将参数格式化为八进制整数。%o",9));
        System.out.println(String.format("'x':将参数格式化为十六进制整数。%x",17));
        System.out.println(String.format("'e':将参数格式化为科学计数法的浮点数，'E'的效果相同。%E",10.000001));
        System.out.println(String.format("'f':将参数格式化为十进制浮点数。%f",10.000001));
        System.out.println(String.format("'g':根据具体情况，自动选择用普通表示方式还是科学计数法方式，'G'效果相同。10.01=%g",10.01));
        System.out.println(String.format("'g':根据具体情况，自动选择用普通表示方式还是科学计数法方式，'G'效果相同。10.00000000005=%g",10.00000000005));
        System.out.println(String.format("'a':结果被格式化为带有效位数和指数的十六进制浮点数，'A'效果相同,但结果中字母为大写。%a",10.1));
        System.out.println(String.format("'t':时间日期格式化前缀，会在后面讲述"));
        System.out.println(String.format("'%%':输出%%。%%"));
        System.out.println(String.format("'n'平台独立的行分隔符。System.getProperty(\"line.separator\")可以取得平台独立的行分隔符，但是用在format中间未免显得过于烦琐了%n已经换行"));
        }

    private static class FormatImpl implements Formattable {
        @Override
        public void formatTo(Formatter formatter, int flags, int width, int precision) {
            formatter.format("我是Formattable接口的实现类");
        }
    }
}

```

输出结果为：

```bash
'b':将参数格式化为boolean类型输出，'B'的效果相同,但结果中字母为大写。false
'h':将参数格式化为散列输出，原理：Integer.toHexString(arg.hashCode())，'H'的效果相同,但结果中字母为大写。fc42
's':将参数格式化为字符串输出，如果参数实现了 Formattable接口，则调用 formatTo方法。'S'的效果相同。16
FormatImpl类实现了Formattable接口：我是Formattable接口的实现类
'c':将参数格式化为Unicode字符，'C'的效果相同。A
'd':将参数格式化为十进制整数。11
'o':将参数格式化为八进制整数。11
'x':将参数格式化为十六进制整数。11
'e':将参数格式化为科学计数法的浮点数，'E'的效果相同。1.000000E+01
'f':将参数格式化为十进制浮点数。10.000001
'g':根据具体情况，自动选择用普通表示方式还是科学计数法方式，'G'效果相同。10.01=10.0100
'g':根据具体情况，自动选择用普通表示方式还是科学计数法方式，'G'效果相同。10.00000000005=10.0000
'a':结果被格式化为带有效位数和指数的十六进制浮点数，'A'效果相同,但结果中字母为大写。0x1.4333333333333p3
't':时间日期格式化前缀，会在后面讲述
'%':输出%。%
'n'平台独立的行分隔符。System.getProperty("line.separator")可以取得平台独立的行分隔符，但是用在format中间未免显得过于烦琐了
已经换行
```

#### argument number:位置参数

```
System.out.println(String.format("Java提供了%1$s类用于格式化，我们可以使用%1$s的%2$s方法格式化字符串。", "java.util.Formatter", "format()"));
```

结果如下：

```bash
Java提供了java.util.Formatter类用于格式化，我们可以使用java.util.Formatter的format()方法格式化字符串。
```

可以使用`<`标识重复使用之前用过的参数

```java
System.out.println(String.format("%s, %<s, %<s", "重复使用参数"));
```

输出如下：

```bash
重复使用参数, 重复使用参数, 重复使用参数
```

#### flags:特定类型选项

我们可以使用flags参数控制特定类型输出的格式，如左对齐，逗号隔开数字。

|符号|说明|
|-|-|
|`'-'`|在最小宽度内左对齐，不可以与“用0填充”同时使用|
|`'+'`|结果总是包括一个符号|
|`' '`|正值前加空格，负值前加负号|
|`'0'`|结果将用零来填充|
|`','`|每3位数字之间用“，”分隔(只适用于fgG的转换)|
|`'('`|若参数是负数，则结果中不添加负号而是用圆括号把数字括起来(只适用于eEfgG的转换)|

```java
    private static void formatFlags() {
        System.out.println("'-':在最小宽度内左对齐，不可与\"用0填充\"同时使用。");
        System.out.println(String.format("设置最小宽度为8为，左对齐。%-8d:%-8d:%-8d%n", 1, 22, 99999999));
        System.out.println(String.format("'0':结果将用零来填充。设置最小宽度为8，%08d:%08d:%08d", 1, -22, 99999990));
        System.out.println(String.format("'+':结果总是包括一个符号。%+d:%+d:%+d", 1, -2, 0));
        System.out.println(String.format("' ':正值前加空格，负值前加负号。% d:% d:% d", 1, -2, 0));
        System.out.println(String.format("',':每3位数字之间用“，”分隔(只适用于fgG的转换)。%,d:%,d:%,d", 1, 100, 1000));
        System.out.println(String.format("'(':若参数是负数，则结果中不添加负号而是用圆括号把数字括起来(只适用于eEfgG的转换)。%(d:%(d", 1, -1));
    }
```

结果如下：

```bash
'-':在最小宽度内左对齐，不可与"用0填充"同时使用。
设置最小宽度为8为，左对齐。1       :22      :99999999

'0':结果将用零来填充。设置最小宽度为8，00000001:-0000022:99999990
'+':结果总是包括一个符号。+1:-2:+0
' ':正值前加空格，负值前加负号。 1:-2: 0
',':每3位数字之间用“，”分隔(只适用于fgG的转换)。1:100:1,000
'(':若参数是负数，则结果中不添加负号而是用圆括号把数字括起来(只适用于eEfgG的转换)。1:(1)
```
#### width:设置宽度

最小字幅宽度，注意，这并非总数，输出可以超过此宽度，不足则会自动补0。该值不能为0。

```java
System.out.println(String.format("'0':结果将用零来填充。设置最小宽度为8，%08d:%08d:%08d", 1, -22, 99999990));
```

输出结果：

```bash
'0':结果将用零来填充。设置最小宽度为8，00000001:-0000022:99999990
```

#### .precision:精确度

用于控制浮点数的精确度。

```java
System.out.println(String.format("设置精度为2位：%.2f", 1f));
```

输出如下：

```bash
设置精度为2位：1.00
```

### 日期时间类型格式化

时间日期格式化的转换符分为三类，时间格式化转换符，日期格式化转换符，时间日期格式化转换符

#### 时间格式化

时间格式化字符串格式为`%[argument number$][flags][width]type`
其中type字段第一个字符固定为T或t。

```java
/**
* 格式化时间
*/

private static void formatTime() {
   System.out.println("这是格式化时间相关的，具体输出跟你执行代码时间有关");
   Calendar calendar = Calendar.getInstance();
   System.out.println(String.format("'H':2位数24小时制，不足两位前面补0：%tH（范围：00-23）", calendar));
   System.out.println(String.format("'I':2位数12小时制，不足两位前面补0：%tI（范围：01-12）", calendar));
   System.out.println(String.format("'k':24小时制，不足两位不补0：%tk（范围：0-23）", calendar));
   System.out.println(String.format("'l':12小时制，不足两位不补0：%tl（范围：1-12）", calendar));
   System.out.println(String.format("'M':2位数的分钟，不足两位前面补0：%tM（范围：00-59）", calendar));
   System.out.println(String.format("'S':分钟中的秒，2位数，不足两位前面补0，60是支持闰秒的一个特殊值：%tS（范围：00-60）", calendar));
   System.out.println(String.format("'L':3位数的毫秒，不足三位前面补0：%tL（范围：000-999）", calendar));
   System.out.println(String.format("'N':9位数的微秒，不足九位前面补0：%tN（范围：000000000-999999999）", calendar));

   System.out.println(String.format("'p':输出本地化的上午下午，例如，Locale.US为am或pm，Locale.CHINA为上午或下午", calendar));
   System.out.println(String.format(Locale.US, "Local.US=%tp", calendar));
   System.out.println(String.format(Locale.CHINA, "Local.CHINA=%tp", calendar));
   System.out.println();

   System.out.println(String.format("'z':时区：%tz", calendar));
   System.out.println(String.format("'Z':时区缩写字符串：%tZ", calendar));
   System.out.println(String.format("'s':从1970-1-1 00:00到现在所经历的秒数：%ts", calendar));
   System.out.println(String.format("'Q':从1970-1-1 00:00到现在所经历的毫秒数：%tQ", calendar));
}

```

结果如下：

```bash
这是格式化时间相关的，具体输出跟你执行代码时间有关
'H':2位数24小时制，不足两位前面补0：18（范围：00-23）
'I':2位数12小时制，不足两位前面补0：06（范围：01-12）
'k':24小时制，不足两位不补0：18（范围：0-23）
'l':12小时制，不足两位不补0：6（范围：1-12）
'M':2位数的分钟，不足两位前面补0：59（范围：00-59）
'S':分钟中的秒，2位数，不足两位前面补0，60是支持闰秒的一个特殊值：44（范围：00-60）
'L':3位数的毫秒，不足三位前面补0：638（范围：000-999）
'N':9位数的微秒，不足九位前面补0：638000000（范围：000000000-999999999）
'p':输出本地化的上午下午，例如，Locale.US为am或pm，Locale.CHINA为上午或下午
Local.US=pm
Local.CHINA=下午

'z':时区：+0800
'Z':时区缩写字符串：CST
's':从1970-1-1 00:00到现在所经历的秒数：1528628384
'Q':从1970-1-1 00:00到现在所经历的毫秒数：1528628384638
```

#### 日期格式化

```java
    private static void formatDate() {
        System.out.println("这是格式化时间相关的，具体输出跟你执行代码时间有关");
        Calendar calendar = Calendar.getInstance();
        System.out.println(String.format("'B':本地化显示月份字符串，如：January、February"));
        System.out.println(String.format("'b':本地化显示月份字符串的缩写，如：Jan、Feb"));
        System.out.println(String.format("'h':本地化显示月份字符串的缩写，效果同'b'"));
        System.out.println(String.format(Locale.US, "Locale.US 月份=%1$tB，缩写=%1$tb", calendar));
        System.out.println(String.format(Locale.CHINA, "Locale.CHINA 月份=%1$tB，缩写=%1$tb", calendar));

        System.out.println(String.format("'A':本地化显示星期几字符串，如：Sunday、Monday"));
        System.out.println(String.format("'a':本地化显示星期几字符串的缩写，如：Sun、Mon"));
        System.out.println(String.format(Locale.US, "Locale.US 星期几=%1$tA，缩写=%1$ta", calendar));
        System.out.println(String.format(Locale.CHINA, "Locale.CHINA 星期几=%1$tA，缩写=%1$ta", calendar));

        System.out.println(String.format("'C':年份除以100的结果，显示两位数，不足两位前面补0：%tC（范围：00-99）", calendar));
        System.out.println(String.format("'Y':显示四位数的年份，格利高里历，即公历。不足四位前面补0：%tY", calendar));
        System.out.println(String.format("'y':显示年份的后两位：%ty（范围：00-99）", calendar));
        System.out.println(String.format("'j':显示当前公历年的天数：第%tj天（范围：001-366）", calendar));
        System.out.println(String.format("'m':显示当前月份：%tm月（范围：01-13？怎么会有13个月？）", calendar));
        System.out.println(String.format("'d':显示是当前月的第几天，不足两位前面补0：%1$tm月第%1$td天（范围：01-31）", calendar));
        System.out.println(String.format("'e':显示是当前月的第几天：%1$tm月第%1$te天（范围：1-31）", calendar));
    }
```

输出如下：

```bash
这是格式化时间相关的，具体输出跟你执行代码时间有关
'B':本地化显示月份字符串，如：January、February
'b':本地化显示月份字符串的缩写，如：Jan、Feb
'h':本地化显示月份字符串的缩写，效果同'b'
Locale.US 月份=June，缩写=Jun
Locale.CHINA 月份=六月，缩写=6月
'A':本地化显示星期几字符串，如：Sunday、Monday
'a':本地化显示星期几字符串的缩写，如：Sun、Mon
Locale.US 星期几=Sunday，缩写=Sun
Locale.CHINA 星期几=星期日，缩写=周日
'C':年份除以100的结果，显示两位数，不足两位前面补0：20（范围：00-99）
'Y':显示四位数的年份，格利高里历，即公历。不足四位前面补0：2018
'y':显示年份的后两位：18（范围：00-99）
'j':显示当前公历年的天数：第161天（范围：001-366）
'm':显示当前月份：06月（范围：01-13？怎么会有13个月？）
'd':显示是当前月的第几天，不足两位前面补0：06月第10天（范围：01-31）
'e':显示是当前月的第几天：06月第10天（范围：1-31）

```

时间日期格式化：

```java
    /**
     * 格式化时间日期
     */
    private static void formatTimeAndDate() {
        System.out.println("这是格式化时间相关的，具体输出跟你执行代码时间有关");
        Calendar calendar = Calendar.getInstance();
        //%tH:%tM的缩写
        System.out.println(String.format("'R':将时间格式化为：HH:MM（24小时制）。输出：%tR", calendar));
        //%tH:%tM:%tS的缩写
        System.out.println(String.format("'T':将时间格式化为：HH:MM:SS（24小时制）。输出：%tT", calendar));
        //%tI:%tM:%tS %Tp的缩写，输出形如：
        System.out.println(String.format("'r':将时间格式化为：09:23:15 下午，跟设置的语言地区有关。输出：%tr", calendar));
        //%tm/%td/%ty的缩写，输出形如
        System.out.println(String.format("'D':将时间格式化为：10/19/16。输出：%tD", calendar));
        //%tY-%tm-%td，输出形如：
        System.out.println(String.format("'F':将时间格式化为：2016-10-19。输出：%tF", calendar));
        //%ta %tb %td %tT %tZ %tY，输出形如：Sun Jul 20 16:17:00 EDT 1969
        System.out.println(String.format("'c':将时间格式化为\"Sun Jul 20 16:17:00 EDT 1969\"。输出：%tc", calendar));
    }

```

输出如下：

```bash
    /**
     * 格式化时间日期
     */
    private static void formatTimeAndDate() {
        System.out.println("这是格式化时间相关的，具体输出跟你执行代码时间有关");
        Calendar calendar = Calendar.getInstance();
        //%tH:%tM的缩写
        System.out.println(String.format("'R':将时间格式化为：HH:MM（24小时制）。输出：%tR", calendar));
        //%tH:%tM:%tS的缩写
        System.out.println(String.format("'T':将时间格式化为：HH:MM:SS（24小时制）。输出：%tT", calendar));
        //%tI:%tM:%tS %Tp的缩写，输出形如：
        System.out.println(String.format("'r':将时间格式化为：09:23:15 下午，跟设置的语言地区有关。输出：%tr", calendar));
        //%tm/%td/%ty的缩写，输出形如
        System.out.println(String.format("'D':将时间格式化为：10/19/16。输出：%tD", calendar));
        //%tY-%tm-%td，输出形如：
        System.out.println(String.format("'F':将时间格式化为：2016-10-19。输出：%tF", calendar));
        //%ta %tb %td %tT %tZ %tY，输出形如：Sun Jul 20 16:17:00 EDT 1969
        System.out.println(String.format("'c':将时间格式化为\"Sun Jul 20 16:17:00 EDT 1969\"。输出：%tc", calendar));
    }
```


> 参考文档：
> [踏歌行---Java 字符串格式化详解](https://www.cnblogs.com/travellife/p/Java-zi-fu-chuan-ge-shi-hua-xiang-jie.html)
> [Java API -- Formatter](https://docs.oracle.com/javase/10/docs/api/java/util/Formatter.html)
> [lonely_fireworks的专栏---JAVA字符串格式化-String.format()的使用](https://blog.csdn.net/lonely_fireworks/article/details/7962171/)