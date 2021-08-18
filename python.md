引用“圣经”共勉
```
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```
再把脑海里冒出Python相关Topic列出来：
- 众所周知，Python2和Python3是两种语言
- 那些魔法函数都是用来施展什么法术的？
- 聊聊不想聊的，GIL、GC、字节码、MRO、元类、异步、生成器

##  众所周知，Python2和Python3是两种语言
引用并“粗翻”(在尊重原文基础上加入“平庸”的思考)官方文档[Python3.0 有什么新变化](https://docs.python.org/zh-cn/3/whatsnew/3.0.html)

> 主要对比的是Python3.0与python2.5版本(这两版本估计都没啥人用了，醉了

### Print 是个函数
`print`语句已经被`print()`函数所取代了，并且使用关键字参数替换旧`print`语句。例子如下：
```python
Old: print "The answer is", 2*2
New: print("The answer is", 2*2)

Old: print x,		    # 尾随逗号来去掉换行符，print语句默认会附加一个"\n"
New: print(x, end=" ")	# end参数表示附加一个空格而不是换行符，由此可见默认值是"\n"

Old: print		        # 打印一个空行
New: print()		    # 必须要加括号调用这个函数了

Old: print >> sys.stderr, "fatal error"
New: print("fatal error", file=sys.stderr) # 重定向

Old: print (x, y)	    # 打印repr((x, y))
New: print((x, y))	    # 跟print(x, y)不一样
```
你也可以自定义待打印参数的分隔符，比如：
```python
print("There are <", 2**32, "> possibilities!", sep="") # sep=""意味着没有分隔符
```
输出
```python
There are <4294967296> possibilities!
```
请注意
- 新`print()`函数不支持旧`print`语句的“软空格”特性。举个例子，在Python 2.x中`print "A\n", "B",会输出"A\nB\n"；但是在Python3.0中,
`print("A\n", "B")会输出成"A\n B\n"。
- 习惯就好
- 使用 2to3 源码转换工具时，所有旧的`print`语句都会自动转换成新的`print()`函数。
> （至于为什么要针对`print`的更改，咱也不知道，咱也懒得去查了

### Views(视图)和Iterators(迭代器)取代了Lists(列表们)
一些常用的APIs不再返回列表了
- dict方法中的`dict.keys(), dict.items()`和`dict.values()`返回"views(视图)"并取代了列表。
举个例子，这个语句不能在照常运行了:`k = d.keys(); k.sort()`。转而用`k = sorted(d)`取代(这也适用于Python2.5，并同样有效)。
- 另外，像dict.iterkeys(), dict.iteritems()和dict.itervalues()等方法也不再支持了。
- map()和filter()返回iterators(迭代器)。如果你真的需要一个列表并且输入序列的长度都相等，一个快速应对方法
是用list()包装map()，就像`list(map(...))`，不过通常更好的做法是使用列表推导式(特别是原始代码是使用lambada的时候)，或者重写待代码
达到根本需要列表的程度。特别棘手的是map()为函数的副作用而调用；正确的转换是使用常规的 for 循环(因为创建列表只会浪费)。
如果输入序列的长度不同，map() 将在最短序列的终止处停止。为了与 Python 2.x 中的 map() 完全兼容，还将序列包装在`itertools.zip_longest()` 中，
例如`map(func, *sequences)` 变成 `list(map(func, itertools.zip_longest(*sequences)))`。
- range() 现在的行为类似于 xrange() 以前的行为，除了它适用于任意大小的值。后者已不复存在。
- zip() 现在返回一个迭代器。
