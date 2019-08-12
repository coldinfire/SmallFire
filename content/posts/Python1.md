---

title: " Python基础 "
date: 2019-01-16T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  -  Python

tags: 
  - python

---

# 变量定义和数据类型

------

## 变量定义

Python的变量命名规则：

- 硬性规则：
  - 变量由字母、数字和下划线构成，不能以数字开头
  - 大小写敏感
  - 不能跟python的关键字和系统保留字冲突
- PEP8要求：
  - 用小写字母拼写，多个单词用下划线连接
  - 受保护的实例属性用单个下划线开头
  - 私有实例属性用两个下划线开头

Python关键字列表：

| **False**  | **class**        | **finally** | **is**       | **return** |
| :--------- | :--------------- | :---------- | :----------- | :--------- |
| **None**   | **continue**     | **for**     | **lambda**   | **try**    |
| **True**   | **def**          | **from**    | **nonlocal** | **while**  |
| **and**    | **del**          | **global**  | **not**      | **with**   |
| **as**     | **if/else/elif** | **or**      | **yield**    | **assert** |
| **import** | **break**        | **pass**    | **except**   | **raise**  |
| **in**     | **_*  __***      | *****       |              |            |

## 数据类型

基本数据类型：

- 整型：对整数的处理

- 浮点型：对小数的处理

- 字符串型：单引号和双引号引起来的任意文本

- 布尔型：True和False两种值，也可通过计算得出

- 复数型：数学表示法`7i+8j`

  

- `int()`：将一个数值或字符串转换成整数，可以指定进制。

- `float()`：将一个字符串转换成浮点数。

- `str()`：将指定的对象转换成字符串形式，可以指定编码。

- `chr()`：将整数转换成该编码对应的字符串（一个字符）。

- `ord()`：将字符串（一个字符）转换成对应的编码（整数）。

复杂数据类型：

- 列表：[ ]，可以存储任意类型，任意数量的变量
- 集合：{ }，可以存储任意类型变量，定以后不能更改其元素
- 字典：{}，以键值对形式存储数据元素


# 运算操作符

------

| **+**  | **和**     | **/**  | **除**   | **<<** | **左移** | **&**  | **按位与**   | **<=** | **...** | is/is not  | 身份运算符 |
| :----- | :--------- | :----- | :------- | :----- | :------- | :----- | :----------- | :----- | :------ | :--------- | ---------- |
| **-**  | **差**     | **//** | **模**   | **>>** | **右移** | **\|** | **按位或**   | **>=** | **...** | in/not in  | 成员运算符 |
| *****  | **积**     | **%**  | **整除** | **<**  | **小于** | **^**  | **按位异或** | **==** | **...** | not/and/or | 逻辑运算符 |
| ****** | **幂运算** | **@**  |          | **>**  | **大于** | **~**  | **按位取反** | **!=** | **...** |            |            |

**在实际开发中，运算会有不同的优先级，可以用括号保证运算的执行顺序。**

# Delimiters
----------
| (  )  | ,    | :    | - >  | *=   | %=   | \|=  | <<=  |
| :---- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [  ]  | .    | ;    | +=   | /=   | @=   | ^=   | **=  |
| {   } | @    | =    | -=   | //=  | &=   | >>=  |      |



# 分支结构

------

​	在Python中，构造分支结构使用if、else和elif关键字。与其他语言不同Python没有使用花括号构建代码块，而是使用其独特的缩进方式来设置代码的层次结构。连续的代码保持相同的缩进那么就属于同一个代码块。

​	分支结构是可以嵌套使用的。

# List

----------
```JS
list.append(x)
list.extend(iterable)
list.insert(i,x)
list.remove(x)
list.pop([i])
from collections import deque  :  deque([])   deque.popleft()
list.clear()   =  del list
list.index(x[,start[,end]])
list.count(x)
list.sort(key = None,reverse=False)
list.reverse()
list.copy() = a[:]
```
# Tuples & Sequences
----------
```JS
1. enumerate(): 
   for i, v in enumerate(['tic', 'tac', 'toe']):
	...     print(i, v)
	...
	0 tic
	1 tac
	2 toe
2. zip()  reversed()  sorted()
3. 
```
# JSON
----------
```JS
python 与 JSON对照：
dict              {}
list              []
str               'string'
int/float         1234.56
True/False        true/false
None              null
>import json
>json.dumps([1, 'simple', 'list'])
>'[1, "simple", "list"]'

pickle : Pickle Module
	    pickle.dump().
	    pickle.load()
```



# 函数(FUN)

----------
![Python内置函数](/images/python/pythonFun.png)

```JS
位置参数：
		 根据参数位置匹配
默认参数：def fun(arg1,arg2,arg = 'xxx')
		 1.定义默认参数时，，默认参数必须指向不变对象
		 2.当函数有多个参数时，将变化大的参数放前面，变化小的放后面。变化小的可以作为默认参数。
	     3. 默认按参数位置匹配，可以通过名字进行相应的默认字段赋值
可变参数：def  fun（arg1,arg2,*numbers）
		 允许传入多个参数，在函数调用时自动组装成一个tuple
		 当参数为list或则tuple时，可以使用*list,*tuple
关键字参数：def fun(arg1,arg2,**kw)
		 允许传入多个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。
		 传入dict时，只是将所有的参数传入函数，对参数的改变不会影响到函数外的dict
命名关键字参数：def fun (arg1,arg2,*,arg3,arg4)
		 对于关键字参数，需要在函数内部进行检查,使用命名关键字必须传入参数名
参数组合：
		参数定义的顺序（必选参数，默认参数，可变参数，命名关键字，关键字参数）
对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的。
```
# 递归函数

----------

**注意栈溢出问题**

​	解决递归调用栈溢出的方法是通过尾递归优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。

**尾递归是指**

​	在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。

# 迭代器（Iterable）
----------
```JS
1. 原理：
	For调用了var = iter(str)，next(var),当next()没有元素时，raise a StopIteration
2. 集合数据类型：list、tuple、dict、set、str等
generator:sum(i*i for i in range(10))
包括生成器和带yield的generator function
		def reverse(data):
		    for index in range(len(data)-1, -1, -1):
		        yield data[index]
```
# 模块(Mod)
----------
```JS
导入整个模块：import modname.   from modname import *.
导入模块部分：from modname import name1[, name2[, ... nameN]].
OS Interface:
	os:getcwd()  chdir()  system() open()
	shutil:copyfile('source','aim')     move('source','aim')
File Wildcards: 
	glob:glob.glog('*.py')
Output Formatting:
	reprlib: reprlib.repr(set('adfasfsadfsfsfsfs'))
	pprint: pprint.pprint((source,width = vaule))
	textwrap:格式化输出段落适应屏幕宽度  textwrap.fill(source,width=value)
	locale:culture specific data formats	
			x = 1234567.8
			locale.format_string("%s%.*f", (conv['currency_symbol'],conv['frac_digits'], x), grouping=True)
			'$1,234,567.80'
Command Line Arg:	
	sys: sys. args  sys.stdin sys.stdout, sys.stderr.write('Error msg') , sys.exit()
	getopt:
	argparse:
String Pattern Matching:
	re:re.fundall(), re.sub  etc.
Templating:
	string(Template):t = Template('${village}folk send $$10 to $cause.')
						 t.substitute(village='Nottingham', cause='the ditch fund')
						>>>'Nottinghamfolk send $10 to the ditch fund.'
Mathematics:
	math:math.cos(),math.log() etc.
	random:random.choice([]),random.sample(range(100),10) etc,
	statistics:(mean,median,variance)etc.
Internet Access:
	urllim.request(retrieving data from URLs):
	smtplib(sending mail): server = smtplib.SMTP('xxx')   server.sendmail('from_email','to_email')  server.quit()
Dates and Times:
	datetime(date) :  date.today()  
Data Compression: (zlib,gzip,bz2,lzma,zipfile,tarfile)
	zlib:zlib.compress('source')   zlib.decompress('zlib_source')
Quality Control:
	doctest: doctest.testmod()
	unittest: unittest.TestCase   assertRaises():   unittest.amin()
Multi-threading:;
	threading: threading.Thread
Logging: 
	logging: sys.stderr(file)   logging.debug()  logging.info()  logging.warning()  logging.error() logging.critical()
Weak References:
	wearkref: 
Tools For Lists:
	array: a = array('H',[1213,1414,4124])   sum(a)-只计算list中的内容，对a的操作只对list有效
	collections(deque):
	bisect:manipulation sorder lists  bisect.insort(aim,source)
	heapq:use for repeatedly access the smallest element do not run a full list sort(堆排序)
			heapify(list)   heappush(list,value)   [headppop(list) for i in range(x)]   
Decimal Floating Point Ath:(精准计算)
	decimal:  
		help for(1)	financial applications and other uses which require exact decimal representation,
				(2)	control over precision,
				(3)	control over rounding to meet legal or regulatory requirements,
				(4)	tracking of significant decimal places, or
				(5)	applications where the user expects the results to match calculations done by hand
```

# 包管理(pip)
```JS
Virtual environments:
1. Create
	On Windows, invoke the venv command as follows:
		python -m venv c:\path\to\myenv
		venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
      			  [--upgrade] [--without-pip]
       			  ENV_DIR [ENV_DIR ...]
2. Activate
	安装名\Scripts\activate.bat
3. Managing Packages  pip(On Virtual)
	https://pypi.org/
	pip search xxxx.(查找)
	pip install xxxx.(安装)
	pip install xxxx=version.(安装指定版本)
	pip install --upgrade xxxx.(更新到最新版本)
	pip show xxxxx.(显示包有关信息)
	pip list:(将显示虚拟环境中安装的所有软件包)
	pip uninstall xxx,xxxx,xxxx.(卸载)
	pip freeze > requirements.txt.(以指定文件格式显示已安装软件包)
```

# 异常(Error)
----------
![异常1](https://i.imgur.com/fIWzpEQ.png)
![异常2](https://i.imgur.com/4B9otUe.png)
![异常3](https://i.imgur.com/xCmfhsi.png)



```JS
1. 捕捉异常：
	try:
	<语句>        #运行别的代码
	except <名字>：
	<语句>        #如果在try部份引发了'name'异常
	except <name1,name2>，<数据>:
	<语句>        #如果引发了'name'异常，获得附加的数据
	else:
	<语句>        #如果没有异常发生
2. 最终处理
	try:
	
	finaly:

	raise:
3. 自定义异常
	class Error(Exception):
	    """Base class for exceptions in this module."""
	    pass
	
	class InputError(Error):
	    """Exception raised for errors in the input.
	
	    Attributes:
	        expression -- input expression in which the error occurred
	        message -- explanation of the error
	    """
	
	    def __init__(self, expression, message):
	        self.expression = expression
	        self.message = message
	
	class TransitionError(Error):
	    """Raised when an operation attempts a state transition that's not
	    allowed.
	
	    Attributes:
	        previous -- state at beginning of transition
	        next -- attempted new state
	        message -- explanation of why the specific transition is not allowed
	    """
	
	    def __init__(self, previous, next, message):
	        self.previous = previous
	        self.next = next
	        self.message = message
```
# 面向对象(OO)
----------
```JS
1. 定义
	类帮助信息：ClassName._doc_查看
	class_suite：有数据属性，类成员，方法组成
	self：代表类的实例，当前对象的地址，而非类，self.class则指向类

	class ClassName:
	   """所有员工的基类"""  #类文档字符串
	   empCount = 0    #数据属性
	   def __init__(self, name, salary):   #该方法接收参数
	      self.name = name
	      self.salary = salary
	      Employee.empCount += 1
	   
	   def displayCount(self):
	     print "Total Employee %d" % Employee.empCount
	 
	   def displayEmployee(self):
	      print "Name : ", self.name,  ", Salary: ", self.salary

2. 添加，删除，修改，访问类的属性
	demo.attr = value1：添加属性
	demo.attr = value2：修改属性
	del demo.attr：删除属性
	getattr(obj,name[,default])：访问对象的属性
	hasattr(obj,name[,default])：检查是否存在一个属性
	setattr(obj,name[,default])：设置一个属性。如果不存在，会创建一个新属性
	delattr(obj,name)：删除属性
3. 内置属性类
	__dict__：类的属性（包含一个字典，由类的数据属性组成）
	__doc__：类的文档字符串
	__name__：类名
	_module_：类定义所在的模块
	_bases_：类的所有父类构成元素
	__del__：析构函数，在队象销毁时被调用
	__class__:是实例的类
4. 对象销毁（垃圾回收）
	采用引用计数来跟踪和回收垃圾
5. 封装
	如果类具有__setattr__()或 __delattr__()方法，则调用此方法而不是直接更新实例字典。
	__getattr__(self,attr1,attr2):
		setattr(self,attr1,attr2)   
	object.__getattriabute__()
	get(self,实例,所有者)    set()  delete()  set_name()
	
	slots():为此变量分配字符串，可迭代，阻止每个实例自动创建__dict__和__weakref__
	
6. 类的继承
	(1) 语法：class 派生类名(basic1,basic2)：...
	      调用：__init_subclass__
	(2)Python内置的@property装饰器就是负责把一个方法变成属性调用
	(3)mixln:主线都是单一继承下来的，例如，Ostrich继承自Bird。但是，如果需要“混入”额外的功能，通过多重继承就可以实现，比如，让Ostrich除了继承自Bird外，再同时继承Runnable
7. 元类
	class OrderedClass(type):
	
	    @classmethod
	    def __prepare__(metacls, name, bases, **kwds):
	        return collections.OrderedDict()
	
	    def __new__(cls, name, bases, namespace, **kwds):
	        result = type.__new__(cls, name, bases, dict(namespace))
	        result.members = tuple(namespace)
	        return result
	
	class A(metaclass=OrderedClass):
	    def one(self): pass
	    def two(self): pass
	    def three(self): pass
	    def four(self): pass
	
	>>> A.members
	('__module__', 'one', 'two', 'three', 'four')
```

# 正则表达式
----------
```JS
1. 匹配
	 \d:匹配一个数字
	 \w:匹配一个字母或数字
	 \s:匹配一个空格（TAB）
	 .:可以匹配任何字符
	 *:表示任意个字符（包括0个）
	 +:表示至少一个字符
	 ?:表示0个或1个字符
	 {n}:表示n个字符
	 {n,m}:表示n-m个字符
	 A|B：可以匹配A或B
	 ^:表示行的开头，^\d表示必须以数字开头。
	 $表示行的结束，\d$表示必须以数字结束。
	 [0-9a-zA-Z\_]：可以匹配一个数字、字母或者下划线；
	 [0-9a-zA-Z\_]+：可以匹配至少由一个数字、字母或者下划线组成的字符串，比如'a100'，'0_Z'，'Py3000'等等；
	 [a-zA-Z\_][0-9a-zA-Z\_]*：可以匹配由字母或下划线开头，后接任意个由一个数字、字母或者下划线组成的字符串，也就是Python合法的变量；
	 [a-zA-Z\_][0-9a-zA-Z\_]{0, 19}：更精确地限制了变量的长度是1-20个字符（前面1个字符+后面最多19个字符）
2. match()方法判断是否匹配，如果匹配成功，返回一个Match对象，否则返回None
	print(re.match(r'^\d{3}\-\d{3,8}$', '010-12345'))
	re.match(r'^\d{3}\-\d{3,8}$','010 12345')
3. 切割 re.split()
	st = re.split(r'[\s\,\;]+', 'a,;;b, c  d')
	print(st)
4. 分组 如果正则表达式中定义了组，就可以在Match对象上用group()方法提取出子串来 0:原始，1:第几个字串
	m = re.match(r'^(\d{3})-(\d{3,8})$','010-12314')
	print(m)
	print(m.group(0))
	print(m.group(1))
	print(m.group(2))
5. 贪婪匹配:正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符.
	print(re.match(r'^(\d+)(0*)$', '102300').groups())
 	加个?就可以让\d+采用非贪婪匹配
	print(re.match(r'^(\d+?)(0*)$', '102300').groups())
 6. 编译：
 	编译正则表达式，如果正则表达式的字符串本身不合法，会报错；
 	用编译后的正则表达式去匹配字符串。
```