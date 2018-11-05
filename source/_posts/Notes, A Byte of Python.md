---
title: Notes, A Byte of Python
categories:
  - Doing
  - Python
  - 
tags:
  - 
  - 
date: 2017-03-25 03:04:10
toc: true
---
* 总结一下$python$的语法对于一个$cpp\ programmer$来说如何快速上手
* 工具查阅。。

<!-- more -->

### 基本类型
#### 数
* 整数(int) `type(1)`
* 长整数(long) `type(1L)`
* 浮点数(float) `type(1.0)` **python不区分单双精度浮点数**
* 复数(complex) `type(2.3+5j)`

#### 字符串(str)
*  **'和"意思相同** `'hello' "hello"`
`'''或者"""`多行引号 **里面随便用'和" 会自动转义** 

* 自然字符串
r或者R前缀 不会转义 `r"new line\n"`
* Unicode字符串
u或者U前缀 `u"This is a unicode string"`

* Tips:
**字符串是不可变的**
**正则表示式 一定要用自然字符串**
**行连接: (下面两个等价)** 
```python
print\
i
print i
```

### 变量
* 命名规则
类似于C/C++或者Java

### 运算符
* `+ - * / % << >> & | ^ ~ < > <= >= == !=` 不变
* `x**y` ==> $x^y$
* //取整除 `5//2.1=2.0`
* 逻辑运算符 `not and or`

* 运算符优先级

|运算符 | 描述 |
|:----:|:----:|
|lambda |lambda表达式|
|or |布尔“或”|
|and| 布尔“与”|
|not| x 布尔“非”|
|in，not in| 成员测试|
|is，is not| 同一性测试|
|<，<=，>，\>=，!=，==| 比较|
| &#124; |按位或|
|^ |按位异或|
|& |按位与|
|<<，>> |移位|
|+，- |加法与减法
|*，/，%|乘法、除法与取余
|+x，-x| 正负号
|~x |按位翻转
|** | 指数
|x.attribute| 属性参考
|x[index]| 下标
|x[index:index]| 寻址段
|f(arguments...)| 函数调用
|(experession,...)| 绑定或元组显示
|[expression,...]| 列表显示
|{key:datum,...}| 字典显示
|\`expression,...\`| 字符串转换

### 控制语句 (不要忘记:)
**else部分是可选的。如果包含else，它总是在循环结束后执行一次，除非遇到break**

* if和while
```python
# !/usr/bin/python
number = 23
running = True
while running:
    guess=int(raw_input("Enter an integer:"))
    if guess == number:
        print("Congratulation, you guessd it.")
        running = False
    elif guess < number:
        print("No, it is a little higher")
    else:
        print("No, it is a little lower")
else:
    print("The while loop is over.")
print("Done")
```

* for, break和continue
```python
for x in range(1, 10):
    if x == 2:
        continue
    if x == 4:
        break
```

### 函数 (不要忘记:)
* 实参传递方式类似Java, 值类型值传递，对象类型引用传递
```python
def maximum(x, y):
    if x > y:
        return x
    else:
        return y
```

* 默认参数
```python
def printWord(word, times = 1):
    print(word * times)
```
* 关键参数
```python
def func(a, b = 5, c = 10):
    print('a is %d, b is %d, c is %d' % (a, b, c))
#####################################################
func(3, 7)
func(25, c = 24)
func(c = 50, a = 100)
```

* DocStrings
文档字符串的惯例是一个多行字符串，它的首行以大写字母开始，句号结尾。第二行是空行，从第三行开始是详细的描述。
```python
def printMax(x, y):
    '''Prints the maximum of two numbers.
    
    The two values must be integers.'''
    
    if x > y:
        print x, 'is maximum'
    else:
        print y, 'is maximum'
##########################################
printMax(3, 5)
print printMax.__doc__
```

### 模块
**每个.py程序都是1个模块(可以类似cpp类一样用.来访问模块内的成员)**

* from..import语句
```python
import sys
print 'The command line arguments are:'
    for i in sys.argv:
        print i
print '\n\nThe PYTHONPATH is', sys.path, '\n'
```
如果你想要直接输入`argv`变量到你的程序中（避免在每次使用它时打sys.），那么你可以使用`from sys import argv`语句。
如果你想要输入所有sys模块使用的名字，那么你可以使用`from sys import *`语句。这对于所有模块都适用。
一般说来，应该避免使用`from..import`而使用`import`语句，因为这样可以使你的程序更加易读，也可以避免名称的冲突。

* 使用模块的`__name__`
```python
if __name__ == '__main__':
    print 'This program is being run by itself'
else:
    print 'I am being imported from another module'
```

* dir()函数
可以使用内建的dir函数来列出模块定义的标识符。标识符有函数、类和变量。
当你为dir()提供一个模块名的时候，它返回模块定义的名称列表。
如果不提供参数，它返回当前模块中定义的名称列表。
```python
>>> import sys
>>> dir() # get list of attributes for current module
['__builtins__', '__doc__', '__name__', 'sys']
>>> a = 5 # create a new variable 'a'
>>> dir()
['__builtins__', '__doc__', '__name__', 'a', 'sys']
>>> del a # delete/remove a name
>>> dir()
['__builtins__', '__doc__', '__name__', 'sys']
```

### 数据结构
#### 列表
```python
list = [1, 2, 3]
list.append(4)
list.pop(0) # del list[0]
list.sort()
print(list)
```

#### 元组
```python
empty = ()
single = (1, )
zoo = ("wolf", "elephant", "penguin")
print "number of animals in the zoo is", len(zoo) # 3
new_zoo = ("monkey", "dolphin", zoo)
print "number of animals in the new zoo is", len(new_zoo) # 3
```
元组最通常的用法是用在打印语句
```python
age = 22
name = 'Swaroop'
print '%s is %d years old' % (name, age)
print 'Why is %s playing with that python?' % name
```

#### 字典
只能使用不可变的对象（比如字符串）来作为字典的键，但是你可以把不可变或可变的对象作为字典的值。
基本说来就是，你应该只使用简单的对象作为键。
```python
ab = { 'Swaroop' : 'swaroopch@byteofpython.info',
       'Larry' : 'larry@wall.org',
       'Matsumoto' : 'matz@ruby-lang.org',
       'Spammer' : 'spammer@hotmail.com'
}
print "Swaroop's address is %s" % ab['Swaroop']
```

#### 序列
列表、元组和字符串都是序列，序列的两个主要特点是索引操作符和切片操作符。
索引操作符让我们可以从序列中抓取一个特定项目。切片操作符让我们能够获取序列的一个切片，即一部分序列

```python
shoplist = ['apple', 'mango', 'carrot', 'banana']
# Indexing or 'Subscription' operation
print 'Item 0 is', shoplist[0]
print 'Item -1 is', shoplist[-1]
# Slicing on a list
print 'Item 1 to 3 is', shoplist[1:3]
print 'Item 2 to end is', shoplist[2:]
print 'Item 1 to -1 is', shoplist[1:-1]
print 'Item start to end is', shoplist[:]
# Slicing on a string
name = 'swaroop'
print 'characters 1 to 3 is', name[1:3]
print 'characters 2 to end is', name[2:]
print 'characters 1 to -1 is', name[1:-1]
print 'characters start to end is', name[:]
#################################################
Item 0 is apple
Item -1 is banana
Item 1 to 3 is ['mango', 'carrot']
Item 2 to end is ['carrot', 'banana']
Item 1 to -1 is ['mango', 'carrot']
Item start to end is ['apple', 'mango', 'carrot', 'banana']
characters 1 to 3 is wa
characters 2 to end is aroop
characters 1 to -1 is waroo
characters start to end is swaroop
```

#### 引用
当你创建一个对象并给它赋一个变量的时候，这个变量仅仅引用那个对象，而不是表示这个对象本身！
也就是说，变量名指向你计算机中存储那个对象的内存。这被称作名称到对象的绑定。
**必须使用切片操作符来取得拷贝**

### 类
#### 类的声明
基本跟cpp差不多，类中定义的变量，类似于cpp中的类的静态成员变量(比如下中的population)
如果你使用的数据成员名称以双下划线前缀比如__privatevar，Python的名称管理体系会有效地把它作为私有变量。
一个惯例，如果某个变量只想在类或对象中使用，就应该以单下划线前缀。
其他的名称都将作为公共的，可以被其他类/对象使用。
记住这只是一个惯例，并不是Python所要求的（与双下划线前缀不同）。
```python
class Person:
    '''Represents a person.'''
    population = 0
    def __init__(self, name):
        '''Initializes the person's data.'''
        self.name = name
        print '(Initializing %s)' % self.name
        # When this person is created, he/she
        # adds to the population
        Person.population += 1
    def __del__(self):
        '''I am dying.'''
        print '%s says bye.' % self.name
        Person.population -= 1
        if Person.population == 0:
            print 'I am the last one.'
        else:
            print 'There are still %d people left.' % Person.population
    def sayHi(self):
        '''Greeting by the person.
        Really, that's all it does.'''
        print 'Hi, my name is %s.' % self.name
    def howMany(self):
        '''Prints the current population.'''
        if Person.population == 1:
            print 'I am the only person here.'
        else:
            print 'We have %d persons here.' % Person.population

###########################################
swaroop = Person('Swaroop')
swaroop.sayHi()
swaroop.howMany()
kalam = Person('Abdul Kalam')
kalam.sayHi()
kalam.howMany()
swaroop.sayHi()
swaroop.howMany()
###########################################
(Initializing Swaroop)
Hi, my name is Swaroop.
I am the only person here.
(Initializing Abdul Kalam)
Hi, my name is Abdul Kalam.
We have 2 persons here.
Hi, my name is Swaroop.
We have 2 persons here.
Abdul Kalam says bye.
There are still 1 people left.
Swaroop says bye.
I am the last one.
```

#### 类的继承
**Python不会自动调用基本类的constructor，你得亲自专门调用它**
```python
class SchoolMember:
    '''Represents any school member.'''
    def __init__(self, name, age):
        self.name = name
        self.age = age
        print '(Initialized SchoolMember: %s)' % self.name
    def tell(self):
        '''Tell my details.'''
        print 'Name:"%s" Age:"%s"' % (self.name, self.age),
class Teacher(SchoolMember):
    '''Represents a teacher.'''
    def __init__(self, name, age, salary):
        SchoolMember.__init__(self, name, age)
        self.salary = salary
        print '(Initialized Teacher: %s)' % self.name
    def tell(self):
        SchoolMember.tell(self)
        print 'Salary: "%d"' % self.salary
class Student(SchoolMember):
    '''Represents a student.'''
    def __init__(self, name, age, marks):
        SchoolMember.__init__(self, name, age)
        self.marks = marks
        print '(Initialized Student: %s)' % self.name
    def tell(self):
        SchoolMember.tell(self)
        print 'Marks: "%d"' % self.marks

##############################################################
t = Teacher('Mrs. Shrividya', 40, 30000)
s = Student('Swaroop', 22, 75)
print # prints a blank line
members = [t, s]
for member in members:
member.tell() # works for both Teachers and Students
##############################################################
(Initialized SchoolMember: Mrs. Shrividya)
(Initialized Teacher: Mrs. Shrividya)
(Initialized SchoolMember: Swaroop)
(Initialized Student: Swaroop)
Name:"Mrs. Shrividya" Age:"40" Salary: "30000"
Name:"Swaroop" Age:"22" Marks: "75"
```

### I/O
#### 文件
```python
poem = '''\
Programming is fun
When the work is done
if you wanna make your work also fun:
use Python!
'''
f = file('poem.txt', 'w') # open for 'w'riting
f.write(poem) # write text to file
f.close() # close the file
f = file('poem.txt') # if no mode is specified, 'r'ead mode is assumed by default
while True:
    line = f.readline()
    if len(line) == 0: # Zero length indicates EOF
        break
    print line, # Notice comma to avoid automatic newline added by Python
f.close() # close the file
```

#### 储存器
Python提供一个标准的模块，称为pickle。
使用它你可以在一个文件中储存何Python对象，之后你又可以把它完整无缺地取出来。这被称为 持久地 储存对象。
还有另一个模块称为cPickle，它的功能和pickle模块完全相同，只不过它是C语言编写的，因此要快得多（比pickle快1000倍）
```python
import cPickle as p
#import pickle as p
shoplistfile = 'shoplist.data'
# the name of the file where we will store the object
shoplist = ['apple', 'mango', 'carrot']
# Write to the file
f = file(shoplistfile, 'w')
p.dump(shoplist, f) # dump the object to a file
f.close()
del shoplist # remove the shoplist
# Read back from the storage
f = file(shoplistfile)
storedlist = p.load(f)
print storedlist
```

### 异常
**else可以和try...except连用，不能只与try...finally连用**
```python
#!/usr/bin/python
# Filename: raising.py
class ShortInputException(Exception):
    '''A user-defined exception class.'''
    def __init__(self, length, atleast):
    Exception.__init__(self)
    self.length = length
    self.atleast = atleast
try:
    s = raw_input('Enter something --> ')
    if len(s) < 3:
        raise ShortInputException(len(s), 3)
    # Other work can continue as usual here
except EOFError:
    print '\nWhy did you do an EOF on me?'
except ShortInputException, x:
    print 'ShortInputException: The input was of length %d, \
was expecting at least %d' % (x.length, x.atleast)
else:
    print 'No exception was raised.'
```

### Python标准库
#### sys模块
* `sys.argv`列表中总是至少有一个项目。`sys.argv[0]`（由于Python从0开始计数）就是当前运行的程序名称。其他的命令行参数在这个项目之后。
* `sys.version`字符串给你提供安装的Python的版本信息
* `sys.stdin、sys.stdout和sys.stderr`分别对应程序的标准输入、标准输出和标准错误流。

#### os模块
* `os.name`字符串指示你正在使用的平台。比如对于Windows，它是'nt'，而对于Linux/Unix
用户，它是'posix'。
* `os.getcwd()`函数得到当前工作目录，即当前Python脚本工作的目录路径。
* `os.getenv()和os.putenv()`函数分别用来读取和设置环境变量。
* `os.listdir()`返回指定目录下的所有文件和目录名。
* `os.remove()`函数用来删除一个文件。
* `os.system()`函数用来运行shell命令。
* `os.linesep`字符串给出当前平台使用的行终止符。例如，Windows使用'\r\n'，Linux使
用'\n'而Mac使用'\r'。
* `os.path.split()`函数返回一个路径的目录名和文件名。
```python
>>> os.path.split('/home/swaroop/byte/code/poem.txt')
('/home/swaroop/byte/code', 'poem.txt')
```
* `os.path.isfile()和os.path.isdir()`函数分别检验给出的路径是一个文件还是目录
* `os.path.exists()`函数用来检验给出的路径是否真地存在

### 更多关于Python
#### 列表综合（List Comprehension）
```python
#!/usr/bin/python
listone = [2, 3, 4]
listtwo = [2*i for i in listone if i > 2]
print listtwo
```

#### 在函数中接收元组和列表
在args变量前有`*`前缀，多余的函数参数都会作为一个元组存储在args中
如果使用的是`**`前缀，多余的参数则会被认为是一个字典的键/值对
```python
>>> def powersum(power, *args):
... '''Return the sum of each argument raised to specified power.'''
... total = 0
... for i in args:
... total += pow(i, power)
... return total
...
>>> powersum(2, 3, 4)
25
```
```python
>>> dict = {}
>>> print dict
{}
>>> def addKVs(**args):
...     for key in args.keys():
...             dict[key] = args[key]
...
>>> addKVs(a=1,b=2)
>>> print dict
{'a': 1, 'b': 2}
```

#### lambda expression
语法：`lambda [arg1[,arg2,arg3....argN]]:expression`
注意：
只能使用表达式，即便是print语句也不能用在lambda形式中
`for..in..if`能做的，最好不要选择lambda
```python
fuck = lambda : 'hello world'
print fuck()
addition = lambda a, b : a + b
print addition(1, 2)
###########################
hello world
3
```

#### exec、eval、assert、repr
* `exec`语句用来执行储存在字符串或文件中的Python语句
```python
>>> exec 'print "Hello World"'
Hello World
```
* `eval`语句用来计算存储在字符串中的有效Python表达式
```python
>>> eval('2*3')
6
```
* `assert`语句用来声明某个条件是真的
```python
>>> mylist = ['item']
>>> assert len(mylist) >= 1
>>> mylist.pop()
'item'
>>> assert len(mylist) >= 1
Traceback (most recent call last):
File "<stdin>", line 1, in ?
AssertionError
```
* `repr`函数和`反引号`（也称转换符）用来获取对象的可打印表示形式
注意，在大多数时候有`eval(repr(object)) == object`
可通过定义类的`__repr__`方法来控制对象被`repr`函数调用时返回的内容

```python
>>> i = []
>>> i.append('item')
>>> `i`
"['item']"
>>> repr(i)
"['item']"
```