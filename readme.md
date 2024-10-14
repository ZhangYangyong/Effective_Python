# 1.培养 Pythonic 思维

```python
import this
```

- 美胜于丑陋（Python 以编写优美的代码为目标）
- 明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似）
- 简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现）
- 复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
- 扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套）
- 间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题）
- 可读性很重要（优美的代码是可读的）
- 即便假借特例的实用性之名，也不可违背这些规则（这些规则至高无上）
- 不要包容所有错误，除非你确定需要这样做（精准地捕获异常，不写 except:pass 风格的代码）
- 当存在多种可能，不要尝试去猜测
- 而是尽量找一种，最好是唯一一种明显的解决方案（如果不确定，就用穷举法）
- 虽然这并不容易，因为你不是 Python 之父（这里的 Dutch 是指 Guido ）
- 做也许好过不做，但不假思索就动手还不如不做（动手之前要细思量）
- 如果你无法向人描述你的方案，那肯定不是一个好方案；反之亦然（方案测评标准）
- 命名空间是一种绝妙的理念，我们应当多加利用（倡导与号召)

## 第1条　查询自己使用的Python版本

```python
import sys

print(sys.version_info)
print(sys.version)
```

~~~
sys.version_info(major=3, minor=10, micro=0, releaselevel='final', serial=0)
3.10.0 | packaged by conda-forge | (default, Nov 10 2021, 13:20:59) [MSC v.1916 64 bit (AMD64)]
~~~

弃用python2

## 第2条　遵循PEP 8风格指南

- 用4个空格标示缩进而不是制表符
- 行长度尽量不超过79个字符
- 空行规范
- 命名规范
- **每件事都应该有简单的做法，而且最好只有一种**
- 采用行内否定，即把否定词直接写在要否定的内容前面，而不要放在整个表达式的前面
    - if a is not b
    - ~~if not a is b~~
- 不要通过长度判断容器或序列是不是空的 if len(xxx) -> if xxx
- 多行的表达式，应该用括号括起来，而不要用\符号续行。
- import语句（含from x import y）总是应该放在文件开头。
- 总是应该使用绝对名称，而不应该根据当前模块路径来使用相对名称
- 如果一定要用相对名称来编写import语句，那就应该明确地写成：from . import foo。
- 文件中的import语句应该按顺序划分成三个部分：首先引入标准库里的模块，
  然后引入第三方模块，
  最后引入自己的模块。属于同一个部分的import语句按字母顺序排列
- 善用格式化工具

## 第3条　了解bytes与str的区别

- Python有两种类型可以表示字符序列：一种是bytes，另一种是str
- bytes实例包含的是原始数据，即8位的无符号值（通常按照ASCII编码标准来显示)

```python
a = b'hello'
print(a)
print(list(a))

```

~~~
b'hello'
[104, 101, 108, 108, 111]
~~~

- str实例包含的是Unicode码点
- str实例不一定非要用某一种固定的方案编码成二进制数据，
  bytes实例也不一定非要按照某一种固定的方案解码成字符串

- 要把Unicode数据转换成二进制数据，必须调用str的encode方法。
  要把二进制数据转换成Unicode数据，必须调用bytes的decode方法

- 调用这些方法的时候，可以明确指出自己要使用的编码方案，
  也可以采用系统默认的方案，通常是指UTF-8
- 编写Python程序的时候，一定要把解码和编码操作放在界面最外层来做，
  让程序的核心部分可以使用Unicode数据来运作
- 程序的核心部分，应该用str类型来表示Unicode数据，并且不要锁定到某种字符编码上面
- 我们通常需要编写两个辅助函数
    - 一个函数，可以把一个str对象或bytes转换成bytes对象，
    - 另一个函数，可以把一个bytes对象转换成str对象

```python
from typing import Union


def str2bytes(s: Union[str, bytes]) -> bytes:
    if isinstance(s, bytes):
        return s
    return bytes(s, encoding='utf-8')


def bytes2str(b: Union[str, bytes]) -> str:
    if isinstance(b, str):
        return b
    return b.decode('utf-8')


if __name__ == '__main__':
    print(str2bytes('abc'))
    print(bytes2str(b'abc'))

```

- 可以用+操作符将bytes添加到bytes，str也可以这样。
- bytes与bytes之间可以用二元操作符（binary operator）来比较大小，str与str之间也可以
- 不能混合操作bytes和str
- 判断bytes与str实例是否相等，总是会评估为假（False)，
  即便这两个实例表示的字符完全相同，它们也不相等
- 格式串可以把bytes嵌入到str中,但是不能反过来，结果也和期望不符,最好不要混用

```python
print('red %s' % b'blue')
# red b'blue'
print(list('red %s' % b'blue'))
# ['r', 'e', 'd', ' ', 'b', "'", 'b', 'l', 'u', 'e', "'"]
# 系统在bytes实例上面调用__repr__方法（参见第75条），
# 然后用这次调用所得到的结果替换格式字符串里的%s，
# 因此程序会直接输出b'blue'，而不是像你想的那样，输出blue本身
# print(b'red %s' % 'blue')

```

- 在文件操作时 rb,wb 用于操作bytes文本 r,w 用于操作str文本
- 如果要从文件中读取（或者要写入文件之中）的是Unicode数据，
  那么必须注意系统默认的文本编码方案。
  若无法肯定，可通过encoding参数明确指定

## 第4条　用支持插值的f-string取代C风格的格式字符串与str.format方法

- 只要条件允许就使用f-string，它比str.format更简单，更易读，更安全
- xxx{}zzz.format(yyy) 偶尔可用,比如用来填充预先定义的占位符

## 第5条　用辅助函数取代复杂的表达式

- 函数调用比表达式更易读，并且可以添加注释
- Python的语法很容易把复杂的意思挤到同一行表达式里，
  这样写很难懂。复杂的表达式，尤其是那种需要重复使用的复杂表达式，应该写到辅助函数里面
- 用if/else结构写成的条件表达式，要比用or与and写成的Boolean表达式更好懂

## 第6条　把数据结构直接拆分到多个变量里，不要专门通过下标访问

- name,age,_ = xxx 这种形式的解包 比 name,age = xxx[0],xxx[1] 更易读
- 可以用在for循环,推导式中

```python
a = ['张三', 18, '男']
b = ['李四', 19, '女']
for name, age, sex in zip(a, b):
    ...

```

## 第7条　尽量用enumerate取代range

## 第8条　用zip函数同时遍历两个迭代器(多个)

- zip得到的是一个迭代器，比较省内存
- 默认的迭代次数是两个迭代器中较短的
- itertools.zip_longest(a,b) 可以让迭代次数是两个迭代器中较长的

```python
from itertools import zip_longest

a = [1, 2, 3, 4, 5]
b = [6, 7, 8]
for _a, _b in zip(a, b):
    print(_a, _b)
    '''
    1 6
    2 7
    3 8
    '''
for _a, _b in zip_longest(a, b, fillvalue='empty'):
    print(_a, _b)
    '''
    1 6
    2 7
    3 8
    4 empty
    5 empty
    fillvalue 默认为None
    '''

```

## 第9条　不要在for与while循环后面写else块

- 循环后面的else块，只会在循环正常结束的时候才会执行，(看起来和字面义上相反),
  偶尔有特殊用途，但一般不建议使用

## 第10条　用赋值表达式减少重复代码

- Python3.8引入了赋值表达式，它允许我们用一个表达式来替换多个赋值语句，
- 海象运算符

```python
names = {
    '张三': 17,
    '李四': 18,
    '王五': 19
}


def check_age(name):
    return names[name]


age = check_age('张三')
if age < 18:
    print('no')
    print(18 - age)
else:
    print('ok')
# 可以看到 age只在 条件成立分支有用
# 使用赋值表示可以减少代码量
# 而且这样赋值,把 a 的范围缩小 一眼就能看明白 a 不会出现在其他分支里
if (a := check_age('张三')) < 18:
    print('no')
    print(18 - a)
else:
    print('ok')


```

# 列表与字典

## 第11条　学会对序列做切片

- 切片操作符：[start:stop:step]
- 凡是实现了__getitem__与__setitem__这两个特殊方法的类都可以切割
- 不要把三个参数都占满
- 如果从0取到end，可以省略start [:stop]
- 如果从start取到最后一个，可以省略end [start:]
- 取前n个元素 [:n],取后n个元素 [-n:], 注意最好在n>0时使用 因为 -0 = 0 可能会导致错误
- 切割时所用的下标可以越界，但是直接访问列表时却不行
  所以在取前几个和后几个元素时,不用担心原有的列表长度不够的问题
- 把切片放在赋值符号的左侧可以将原列表中这段范围内的元素用赋值符号右侧的元素替换掉，
  但可能会改变原列表的长度

## 第12条　不要在切片里同时指定起止下标与步进

- a[1:10:2] 不如分开写 a[1:10] a[::2] 特别是当参数中有负数时
- 避免使用负步长,如果要使用,可以先a[::-1]取反后再切片
-

## 第13条　通过带星号的unpacking操作来捕获多个元素，不要用切片

- a,*,b = [1,2,3,4] -> a=1,b=4
- a *b,c = [1,2,3,4] -> a=1,b=[2,3],c=4
- 在使用这种写法时，至少要有一个普通的接收变量与它搭配
- 单层结构来说，同一级里面最多只能出现一次带星号的unpacking
- 不推荐对嵌套结构使用这种写法

## 第14条　用sort方法的key参数来表示复杂的排序逻辑

- 内置的列表类型提供了名叫sort的方法

```python
a = [4, 3, 1, 2]
a.sort()
```

- 是原地修改
- sorted()函数则 不会修改原列表
- 凡是具备自然顺序的内置类型几乎都可以用sort方法排列，例如字符串
- 对于自定义类型

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.id = id(self)

    def __repr__(self):
        return f"{type(self).__name__}(name={self.name}, age={self.age})"


persons = [
    Person("John", 25),
    Person("Mary", 30),
    Person("Bob", 40),
    Person("Alice", 35),
]

persons.sort()

```

~~~
Traceback (most recent call last):
  File "D:\Code\pyga\eee.py", line 18, in <module>
    persons.sort()
TypeError: '<' not supported between instances of 'Person' and 'Person'
~~~

- 可以指定key参数，让sort方法根据指定的函数来决定排序的顺序

```python
persons = ...
persons.sort(key=lambda p: p.age)
print(persons)
persons.sort(key=lambda p: p.name)
print(persons)
persons.sort(key=lambda p: p.id)
print(persons)
```

- 多重排序标准

```python
Person = ...
persons = [
    Person("John", 25),
    Person("Mary", 30),
    Person("Bob", 40),
    Person("Boe", 25),
    Person("Alice", 25),
]

# 先按age排序, 再按name排序
# 利用sort对元祖排序规则,两个元祖比较,先比较元祖的第一个元素,相同则比较第二个元素
persons.sort(key=lambda p: (p.age, p.name))  # 按顺序定义构建的元祖
print(persons)
```

- 这种方案适合于简单的排序，都是升序,而且要求类型中的属性是可比较的
- persons.sort(key=lambda p: (-p.age, p.name)) 可以对部分元素取反,实现不同的排序
- 但是name 是字符串，所以不能直接用负号
- 直接定义元素如何比较

```python
class Person:
    def __init__(self, name: str, age):
        self.name = name
        self.age = age
        self.id = id(self)

    def __repr__(self):
        return (f"{type(self).__name__}"
                f"(name={self.name}, age={self.age}, id={self.id})")

    def __lt__(self, other):
        """按名称
        """
        return self.name < other.name  # string类 实现了__lt__ 所以可用


persons = [
    Person("John", 25),
    Person("Mary", 30),
    Person("Bob", 40),
    Person("Boe", 25),
    Person("Alice", 25),
]

persons.sort()
print(persons)

```

~~~
[Person(name=Alice, age=25, id=2202289617312),
 Person(name=Bob, age=40, id=2202289617504),
 Person(name=Boe, age=25, id=2202289617408),
 Person(name=John, age=25, id=2202286774672),
 Person(name=Mary, age=30, id=2202286496336)]
~~~

## 第15条　不要过分依赖给字典添加条目时所用的顺序

- 在Python 3.5与之前的版本中, 字典的顺序是随机的，在Python 3.6中，字典的顺序是插入的顺序
- 确保迭代顺序和插入顺序一致的类型是collections.OrderedDict

- 它的行为跟（Python 3.7以来的）标准dict类型很像，但性能上有很大区别。
  如果要频繁插入或弹出键值对（例如要实现least-recently-used缓存)
  那么OrderedDict可能比标准的Python dict类型更合适 参见第70条
- 处理字典的时候，不能总是假设所有的字典都能保留键值对插入时的顺序
- 特别是类型是类似字典的,可能底层的实现不能保证顺序
- 比如继承自collections.abc.MutableMapping的用户自定义字典类型
- 如何应对
    - 第一，不要依赖插入时的顺序编写代码；
    - 第二，在程序运行时明确判断它是不是标准的字典；
    - 第三，给代码添加类型注解并做静态分析 (这种依赖于外部工具的提示,运行时不保证)

## 第16条　用get处理键不在字典中的情况，不要使用in与KeyError

```python
some_dict = {'a': 1, 'b': 2, 'c': 3}
if 'd' in some_dict:
    num = some_dict['d']
else:
    num = 4

# 有时候会有这样的逻辑,先判断一个键是否存在,再取值,如果不存在,另外处理类型

# 或者下面这样的逻辑 遍历另一个对象,然后从字典中拿值,但是无法保证键存在
keys = ['a', 'b', 'c', 'd', 'e']
for key in keys:
    if key in some_dict:
        print(some_dict[key])
    else:
        some_dict[key] = 0
        print(some_dict[key])
# 可以用KeyError来处理
try:
    print(some_dict['f'])
except KeyError:
    print(0)

# 如果获取一个不确定的键值,推荐使用get方法
# 这个方法在key不存在的时候,返回默认值
if (num := some_dict.get('g', None)) is None:  # 结合赋值表达式,非常简洁
    some_dict['g'] = 0

# dict类型提供了setdefault方法，
# 能够继续简化代码。
# 这个方法会查询字典里有没有这个键，如果有，就返回对应的值；
# 如果没有，就先把用户提供的默认值跟这个键关联起来并插入字典，然后返回这个值
print(some_dict.setdefault('h', -1))
print(some_dict)

```

- setdefault 一般不推荐使用,一是名称不好,二是当尝试使用其返回值时,容易出错

```python
dict_1 = {}
key = "key"
value = []
# 此时 没有这个key,添加这个键值对,并且返回值 是 字典中的值的引用
r = dict_1.setdefault(key, value)
print(r)  # []
r.append(1)
print(value)  # [1]
print(dict_1)  # {'key': [1]}

r2 = dict_1.setdefault(key, [])  # 添加这个键值对,并且返回值 还是 字典中的值的引用
print(r2)  # [1]
r2.append(1)
print(dict_1)  # {'key': [1, 1]}
print(value)  # [1, 1]
# 当传递可变对象的时候 容易引起 浅拷贝带来的问题


```

## 第17条　用defaultdict处理内部状态中缺失的元素，而不要用setdefault

- 如果字典由自己创建，可以使用defaultdict来处理缺失情况

```python
from collections import defaultdict

ids = [1, 1, 2, 2, 3, 3, 4, 5]
# 字典中记录的是索引在哪些位置
dict_ = {
}
# 现在要更新这个字典
for index, i in enumerate(ids):

    if i in dict_:
        dict_[i].add(index)
    else:
        dict_[i] = {index, }

print(dict_)

# 使用defaultdict 可以简化


dict_2 = defaultdict(set)
for index, i in enumerate(ids):
    dict_2[i].add(index)
print(dict_2)
# 这个的问题是默认值是空集合，如果访问不存在的key，也会直接生成并更新
print(dict_2[100])
print(dict_2)

```

## 第18条　学会利用__missing__构造依赖键的默认值

- 利用__missing__方法可以构造依赖键的默认值

```python
class MyDict(dict):
    def __missing__(self, key):
        print(f'missing key:{key} set to default')
        self[key] = 'default'
        return self[key]


dict1 = MyDict([(1, 1), (2, 2)])
print(dict1)
print(dict1[3])
print(dict1[3])
print(dict1)

```

# 函数

## 第19条　不要把函数返回的多个数值拆分到三个以上的变量中

- 不要滥用元组的解包,函数返回太多值
- 返回的值的数量太多的时候,就容易搞错顺序,另外代码长度上也会显得臃肿
- 拆分的值确实很多，那最好还是定义一个轻便的类或namedtuple（参见第37条)

```python
def foo(list_):
    nmax = max(list_)
    nmin = min(list_)
    avg = sum(list_) / len(list_)
    lenlsit = len(list_)
    return nmax, nmin, avg, lenlsit


n_list = [1, 2, 3, 4, 5]
nmax, nmin, avg, lenlsit = foo(n_list)


class ListInfo:
    __slot__ = ['nmax', 'nmin', 'avg', 'lenlsit']

    def __init__(self, nmax, nmin, avg, lenlsit):
        self.nmax = nmax
        self.nmin = nmin
        self.avg = avg
        self.lenlsit = lenlsit


def foo2(list_):
    nmax = max(list_)
    nmin = min(list_)
    avg = sum(list_) / len(list_)
    lenlsit = len(list_)
    return ListInfo(nmax, nmin, avg, lenlsit)


n_list = [1, 2, 3, 4, 5]
nmax, nmin, avg, lenlsit = foo(n_list)

listinfo = foo2(n_list)
print(listinfo.nmax, listinfo.nmin, listinfo.avg, listinfo.lenlsit)

```

## 第20条　遇到意外状况时应该抛出异常，不要返回None

- 多点返回尽量使返回值的类型一致

```python
def foo(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        return None


# 调用者有可能这样处理
for _a, _b in [(1, 2), (2, 0), (0, 2)]:
    if res := foo(_a, _b):  # 当结果已经是0的时候,也不能进入这个分支,不够严谨
        print(f'{_a}/{_b} = {res}')
    else:
        print(f'{_a}/{_b}  除数不能为0')
'''
1/2 = 0.5
2/0  除数不能为0
0/2  除数不能为0
'''
```

- 可以直接选择抛出异常,调用方来处理,最好是给与明确的异常提示

````python
from typing import Union

Num = Union[int, float]


def foo(a: Num,
        b: Num) -> Num:
    """
    Raises:
        ZeroDivisionError
    """
    return a / b
````

- 通过类型注解可以明确禁止函数返回None，即便在特殊情况下，它也不能返回这个值

## 第21条　了解如何在闭包里面使用外围作用域中的变量

- 函数内部可以再定义函数

```python
# 排序一个数组,但是偶数永远在前
from typing import List


def sort(list1: List[int]) -> None:
    def helper(x: int) -> int:
        return x % 2  # 偶数是0 奇数是1

    list1.sort(key=helper)


a = list(range(10))
sort(a)
print(a)

```

- Python解释器对变量名的查找顺序

* 1）当前函数的作用域。
* 2）外围作用域（例如包含当前函数的其他函数所对应的作用域）。
* 3）包含当前代码的那个模块所对应的作用域（也叫全局作用域，global scope）。
* 4）内置作用域（built-in scope，也就是包含len与str等函数的那个作用域

```python
# 排序一个数组,但是偶数永远在前
from typing import List


def sort(list1: List[int]) -> None:
    a, b = 0, 0

    def helper(x: int) -> int:
        return x % 2  # 偶数是0 奇数是1

    def printer() -> None:
        print(list1)  # 可以直接使用父级变量

    def nolocal_test():
        nonlocal a
        a = 1
        b = 1

    list1.sort(key=helper)
    printer()
    nolocal_test()
    print(a, b)  # 1,0 # 同名变量要修改的的话 需要先声明nonlocal


a = list(range(10))
sort(a)
```

- 一般不要使用nonlocal语句
- 如果用法比较复杂,应该使用一个小类来封装状态和提供功能

```python
# 排序一个数组,但是偶数永远在前
from enum import Enum
from typing import List


class NumType(Enum):
    even_number = 0
    odd_number = 1


class Importance:
    def __init__(self, num_type: 'NumType'):
        self.num_type = num_type

    def __call__(self, x: 'int'):
        if self.num_type == NumType.even_number:
            return x % 2 != 0  # 奇数余1 返回Ture,于是排序在False后面
        else:
            return x % 2 == 0


def sort(list1: List[int]) -> None:
    a, b = 0, 0

    def helper(x: int) -> int:
        return x % 2  # 偶数是0 奇数是1

    def printer() -> None:
        print(list1)  # 可以直接使用父级变量

    def nolocal_test():
        nonlocal a
        a = 1
        b = 1

    list1.sort(key=helper)
    printer()
    nolocal_test()
    print(a, b)  # 1,0 # 同名变量要修改的的话 需要先声明nonlocal
    print('-' * 20)
    importance1 = Importance(NumType.odd_number)
    list1.sort(key=importance1)
    printer()
    print('-' * 20)
    importance2 = Importance(NumType.even_number)
    list1.sort(key=importance2)
    printer()


a = list(range(10))
sort(a)

```

## 第22条　用数量可变的位置参数给函数设计清晰的参数列表

- def foo(*args):...

## 第23条　用关键字参数来表示可选的行为

- 将关键字参数一般设定好默认值,然后含义一般是可以调整的选项
- def f1(a, b, *args, kwarg) * 号后面的参数调用的时候就必须使用关键字传参了

## 第24条　用None和docstring来描述默认值会变的参数

- 如果关键字参数的默认值属于这种会发生变化的值，那就应该写成None，
- 并且要在docstring里面描述函数此时的默认行为

## 第25条　用只能以关键字指定和只能按位置传入的参数来设计清晰的参数列表

## 第26条　用functools.wraps定义函数修饰器

```python
# 写一个装饰器用来描述输入和输出
def log(func):
    def wrapper(n):
        print(f'{func.__name__}({n}) -> {(res := func(n))}')
        return res

    return wrapper


# 实现一个斐波那契函数
@log
def fib(n: int):
    """斐波那的第n项的值,从第'1'项开始

    :param n: 项数
    :return: 第n项的值
    """
    if n <= 2:
        return 1
    else:
        return fib(n - 1) + fib(n - 2)


print(fib)
# <function log.<locals>.wrapper at 0x0000025C511FB370>
# <function fib at 0x00000237F246A290>

help(fib)
""" 使用装饰器
Help on function wrapper in module __main__:

wrapper(n)
"""
# ----------------------------------------------------------------------
""" 原函数
Help on function fib in module __main__:

fib(n: int)
    斐波那的第n项的值,从第'1'项开始
    
    :param n: 项数
    :return: 第n项的值
"""

```

- 直接使用装饰器后,帮助文档和函数名都变了,不利于调试阅读

```python
# 写一个装饰器用来描述输入和输出
from functools import wraps


def log(func):
    @wraps(func)  # 加入wrap函数的元数据 可以复制文档
    def wrapper(n):
        print(f'{func.__name__}({n}) -> {(res := func(n))}')
        return res

    return wrapper


fib = ...

print(fib)
help(fib)
```

~~~
<function fib at 0x0000025B6BE8D900>
Help on function fib in module __main__:

fib(n: int)
    斐波那的第n项的值,从第'1'项开始
    
    :param n: 项数
    :return: 第n项的值


进程已结束,退出代码0

~~~

# 第4章　推导与生成

## 第27条　用列表推导取代map与filter

```python
# 列表推导式
a = [1, 2, 3, 4, 5]
# 迭代并生成新的项
b = [i * 2 for i in a]
print(b)

# 过滤掉一些项目
c = [i for i in a if i % 2 == 0]
print(c)

# 组合使用
d = [i * 2 for i in a if i % 2 == 0]

```

- 字典和集合也可以使用推导式创建

```python
some = ...
e = {k: v for k, v in some}  # 字典推导式
f = {i for i in some}  # 集合推导式
g = (i for i in some)  # 没有元组推导式,这种写法是个生成器

```

## 第28条　控制推导逻辑的子表达式不要超过两个

- 推导的时候可以使用多层循环，每层循环可以带有多个条件。
- a = [x for row in matrix for x in row if sum(row) > 6 if x % 2 == 0]
- 过多的层级难以理解,不如直接写成for循环和辅助函数

## 第29条　用赋值表达式消除推导中的重复代码

## 第30条　不要让函数直接返回列表，应该让它逐个生成列表里的值

````python
import time
from typing import Generator


def fibonacci_2(n):
    if n == 0:
        return [0]
    elif n == 1:
        return [0, 1]
    else:
        fib_sequence = [0] * n
        fib_sequence[0] = 0
        fib_sequence[1] = 1
        for i in range(2, n):
            fib_sequence[i] = fib_sequence[i - 1] + fib_sequence[i - 2]
        return fib_sequence


def fibonacci_generator_n(n: int):
    a, b = 0, 1
    count = 0
    while count < n:
        yield a
        a, b = b, a + b
        count += 1


t = time.time()
a = fibonacci_2(10000)
for i in a:
    ...
print(time.time() - t)

t = time.time()
for i in fibonacci_generator_n(10000):
    ...
print(time.time() - t)
# 项数再多一些 内存就满了, 生成器可以避免,而且速度上也有优势
````

## 第31条　谨慎地迭代函数所收到的参数

- 如果函数接受的参数是个包含许多对象的列表，那么这份列表有可能要迭代多次

````python
# 生成器是会耗尽的
# 先写一个临时文件作为数据源
import os

with open('test.txt', 'w', encoding='utf-8') as f:
    f.writelines([f'我是第{i}行\n' for i in range(10)])


def read_file(file_name):
    with open(file_name, 'r', encoding='utf-8') as f:
        for line in f:
            yield line


lines = read_file('test.txt')
l1 = list(lines)  # 这个里面会有实际的数据
l2 = list(lines)  # 再次使用将不会有数据


# 避免对生成器的重复使用
# 可以再每次要要使用的时候重新生成一个生成器
def read_file2(file_name):
    return read_file(file_name)


l3 = list(read_file2('test.txt'))
l4 = list(read_file2('test.txt'))

os.remove('test.txt')

````

- 上述写法可以改进,使用一个实现了__iter__的类来代替

```python
class FileIter:
    def __init__(self, file_path):
        self.file_path = file_path

    def __iter__(self):
        with open(self.file_path, 'r', encoding='utf-8') as f:
            for line in f:
                yield line


lines = FileIter('test.txt')

l1 = list(lines)
l2 = list(lines)

```

- Python的for循环及相关的表达式，正是按照迭代器协议来遍历容器内容的,或者list之类的类在使用
  这种对象的时候会自动触发__iter__方法
- 实现了__iter__方法的类型称为可迭代对象,obj.__iter __() 和 iter(obj) 是等价的
- 上述例子中在每一次遍历时,都调用了一次__iter__方法,重新打开一次文件,所以每次都是全新的迭代器

## 第32条　考虑用生成器表达式改写数据量较大的列表推导

- 生成器表达式和列表推导式类似,区别是使用一对(...)括号 (i for i in xxx)
- 当列表的大小较大时,使用生成器表达式可以避免创建一个较大的列表,优化内存使用
  例如，我们要读取一份文件并返回每行的字符数。假如采用列表推导来实现，
  那需要把文件中的每行文本都载入内存，并统计长度。对于庞大的文件，
  或者像network socket这样无休止的输入来说，这么写恐怕有问题。
- 除了直接使用for循环对生成器进行迭代，还可以用next()函数获取生成器的下一个值。

```python
it = (i for i in range(10))
print(next(it))
print(next(it))

```

- 生成器有状态,使用完毕后不能继续迭代
- 对已经迭代过的生成器继续迭代或使用next(),会抛出StopIteration异常
- 生成器可以组合嵌套

```python
it = (i for i in range(10))
it2 = (i ** 2 for i in it)
print(list(it2))

```

## 第33条　通过yield from把多个生成器连起来用

- 对于使用生成器表达式不方便的例子,可以使用yield语句来定义生成器
- yield from语句可以用来连接生成器

```python
def g1():
    for i in range(1000):
        yield i


def g2():
    for i in range(1000):
        yield i ** 2


def g3():
    yield from g1()
    yield from g2()


def t1():
    for i in g1():
        print(i)
    for i in g2():
        print(i)


def t2():
    for i in g3():
        print(i)


t1()
t2()

```

- yield from语句性能也比自己写for循环要好

## 第34条　不要用send给生成器注入数据

- yield 语句是有返回值的

```python
def g1(multiplier: int, steps: int):
    for i in range(steps):
        a = yield i * multiplier
        print(f'a = {a}')


def main():
    # 这样子只能按照固定的倍率来产生值
    g = g1(3, 5)
    for i in g:
        print(i)


if __name__ == '__main__':
    main()
# for 内部是调用的next()函数,此时 yield 的返回值是None
```

- 可以通过send语句给生成器来推进生成器,此时返回值为send注入的数据

```python
def g1(multiplier: int, steps: int):
    for i in range(steps):
        a = yield i * multiplier
        print(f'a = {a}')


def main():
    # 这样子只能按照固定的倍率来产生值
    g = g1(3, 5)

    aa1 = g.send(None)  # 第一次调用，必须传入None  称为预激
    print(aa1)
    # 此时aa 的值为 第一次yield出来的值

    # 此后再次调用，则可以send任意值 传入的值会被生成器yield语句左边的变量接收
    aa2 = g.send('xxx')
    print(aa2)


if __name__ == '__main__':
    main()

```

- send方法可以把参数发给生成器，让它成为上一条yield表达式的求值结果，
- 并将生成器推进到下一条yield表达式，然后把yield右边的值返回给send方法的调用者
- 刚开始推进生成器的时候，它是从头执行的，而不是从某一条yield表达式那里继续的，
- 所以，首次调用send方法时，只能传None

```python
from math import pi, sin
from typing import Generator, Any
from matplotlib import pyplot as plt

Nums = int | float


def wave(amp: 'Nums',
         steps: 'Nums') -> 'Generator[float, Any, None]':
    """用一个生成器模拟调频信号发生器"""
    step_size = 2 * pi / steps
    for step in range(steps):
        radians = step * step_size
        output = amp * sin(radians)
        yield output


# 现在可以用固定的振幅来生成一系列的值
def run(it: 'Generator[float, Any, None]'):
    res = []
    for out in it:
        print(f'{out:.1f}')
        res.append(out)
    return res


if __name__ == '__main__':
    _res = run(wave(3.0, 800))
    plt.plot(_res)
    plt.show()

```

- 使用send

```python
from math import pi, sin
from typing import Generator, Any
from matplotlib import pyplot as plt

Nums = int | float


def wave(steps: 'Nums') -> 'Generator[float, Any, None]':
    """用一个生成器模拟调频信号发生器"""
    amp = yield
    step_size = 2 * pi / steps
    for step in range(steps):
        radians = step * step_size
        output = amp * sin(radians)
        amp = yield output


def run(steps: 'Nums'):
    it = wave(steps)
    it.__next__()
    res = []
    for step in range(steps):
        out = it.send(step)
        res.append(out)
    return res


if __name__ == '__main__':
    _res = run(800)
    plt.plot(_res)
    plt.show()

```

- 这种写法有个缺点，就是它很难让初次阅读这段代码的人立刻理解它的意思。
- 把yield放在赋值语句的右侧，本身就不太直观，
- 因为我们必须先了解生成器的这项高级特性，才能明白yield与send是如何相互配合的。

- 推荐使用领一个迭代器来传入数据,在原有的逻辑需要传入的数据的部分改为从迭代器中取数据

```python
from math import pi, sin
from typing import Generator, Any, List
from matplotlib import pyplot as plt

Nums = int | float


def wave(steps: 'Nums',
         amps: 'Generator[float, None, None]'
         ) -> 'Generator[float, None, None]':
    """用一个生成器模拟调频信号发生器"""
    step_size = 2 * pi / steps
    for step in range(steps):
        amp = next(amps)
        radians = step * step_size
        output = amp * sin(radians)
        yield output


def run(steps: 'Nums') -> 'List[float]':
    amps = (i for i in range(steps))
    it = wave(steps, amps)
    res = []
    for out in it:
        res.append(out)
    return res


if __name__ == '__main__':
    _res = run(800)
    plt.plot(_res)
    plt.show()

```

## 第35条　不要通过throw变换生成器的状态

- 生成器还有一项高级功能,调用者通过throw方法传来的Exception实例重新抛出
- 略

## 第36条　考虑用itertools拼装迭代器与生成器

### 连接多个迭代器

- Python内置的itertools模块里有很多函数，可以用来安排迭代器之间的交互关系
- 连接多个迭代器
- chain可以把多个迭代器从头到尾连成一个迭代器
- repeat可以制作这样一个迭代器，它会不停地输出某个值
- cycle可以制作这样一个迭代器，它会循环地输出某段内容之中的各项元素。
- tee可以让一个迭代器分裂成多个平行的迭代器，具体个数由第二个参数指定
- zip_longest

```python
import itertools

it = itertools.chain([1, 2, 3], '123')
print(list(it))

```

```python
import itertools

it = itertools.repeat([1, 2, 3], 5)
print(list(it))

```

````python
import itertools

it = itertools.cycle([1, 2, 3])
a = [next(it) for _ in range(10)]
print(a)

````

```python
import itertools

it = itertools.tee([1, 2, 3], 3)

print(list(it[0]))
print(list(it[1]))
print(list(it[2]))
```

### 过滤源迭代器中的元素

- islice可以在不拷贝数据的前提下，按照下标切割源迭代器
- takewhile会一直从源迭代器里获取元素，直到某元素让测试函数返回False为止
- 与takewhile相反，dropwhile会一直跳过源序列里的元素，直到某元素让测试函数返回True为止，然后它会从这个地方开始逐个取值
- filterfalse和内置的filter函数相反，它会逐个输出源迭代器里使得测试函数返回False的那些元素。

### 用源迭代器中的元素合成新元素

- accumulate会从源迭代器里取出一个元素，
  并把已经累计的结果与这个元素一起传给表示累加逻辑的函数，然后输出那个函数的计算结果，
  并把结果当成新的累计值
- product会从一个或多个源迭代器里获取元素，并计算笛卡尔积
- permutations会考虑源迭代器所能给出的全部元素，
  并逐个输出由其中N个元素形成的每种有序排列（permutation）方式，
  元素相同但顺序不同，算作两种排列
- combinations会考虑源迭代器所能给出的全部元素，
  并逐个输出由其中N个元素形成的每种无序组合（combination）方式，
  元素相同但顺序不同，算作同一种组合
- combinations_with_replacement与combinations类似，但它允许同一个元素在组合里多次出现

# 类与接口

## 第37条　用组合起来的类来实现多层结构，不要用嵌套的内置类型

- 不要在字典里嵌套字典、长元组，以及用其他内置类型构造的复杂结构
- namedtuple能够实现出轻量级的容器，以存放不可变的数据，而且将来可以灵活地转化成普通的类。
- 或者使用dataclass
- 当发现嵌套层级变多时应该及时重构

## 第38条　让简单的接口接受函数，而不是类的实例

- python的函数的参数可以是另一个函数,这种作为参数传入的函数有时被称为hook(钩子)
- hook一般用无状态的函数来实现,比定义成类要简单

```python
from collections import defaultdict

names = ['John', 'Alice', 'Bob', 'Emily']


# 根据第二个字母进行排序的辅助函数
def sort_tools(name: 'str'):
    return name[1]


# 根据辅助函数进行排序
names.sort(key=sort_tools)
print(names)


# 定制defaultdict类的行为
def log_missing():
    print('key is missing')
    return 0  # 作为默认的值


d = defaultdict(log_missing)

print(d['1'])
print(d)
```

- 也可以实现一个类的__call__方法,这样这个类的实例就和函数一样都是callable对象

## 第39条　通过@classmethod多态来构造同一体系中的各类对象

- 在Python中，不仅对象支持多态，类也支持多态
- 如果想在超类中用通用的代码构造子类实例，
  那么可以考虑定义@classmethod方法，并在里面用cls(...)的形式构造具体的子类对象

- 实现一套 MapReduce 流程
- 用一个通用的类来表述输入数据 InputData类
- read方法返回输入的字符串,由子类去具体实现

```python
import abc


class InputData:
    """输入数据的超类"""

    @abc.abstractmethod
    def read(self) -> 'str':
        """读取输入数据"""
        ...

```

- 实现一个从文件路径读取input_data 的 PathInputData类

```python
InputData = ...


class PathInputData(InputData):
    def __init__(self, path: str):
        self.path = path

    def read(self) -> 'str':
        with open(self.path, 'r') as f:
            return f.read()
```

- 除了输入数据要通用，我们还想让处理MapReduce任务的工作节点（Worker）
  也能有一套通用的抽象接口，这样不同的Worker就可以通过这套标准的接口来消耗输入数据。

```python
abc = ...


class Worker:
    def __init__(self, input_data: 'InputData'):
        self.input_data = input_data
        self.result = 0

    @abc.abstractmethod
    def map(self):
        pass

    @abc.abstractmethod
    def reduce(self, other):
        pass

```

- 定义一种具体的子类 LineCountWorker 统计每份数据中的换行符个数,然后计算所有数据的总和

```python
Worker = ...


class LineCountWorker(Worker):
    """统计一个文件中换行符的个数"""

    def __init__(self, input_data):
        super().__init__(input_data)

    def map(self):
        data = self.input_data.read()
        self.result = data.count('\n')

    def reduce(self, other: 'Worker'):
        return self.result + other.result
```

- 具体应用
- 从一个目录中读取文件并统计行数

```python
def build_input_data(_dir: 'Path') -> 'Iterator[PathInputData]':
    for i in os.listdir(_dir):
        yield PathInputData(os.path.join(_dir, i))


def creat_works(inputs: 'Iterator[InputData]'):
    return [LineCountWorker(i) for i in inputs]


def execute(works: 'List[Worker]'):
    threads = [Thread(target=i.map) for i in works]
    for i in threads:
        i.start()
    for i in threads:
        i.join()
    first, *rest = works
    for i in rest:
        first.reduce(i)
    return first.result


def map_reduce(temp_dir: 'Path'):
    # 首先编写一个辅助函数从目录中构建多个PathInputData实例
    inputs = build_input_data(temp_dir)
    # 再编写一个函数从每个InputData创建LineCountWorker
    works = creat_works(inputs)
    # 最后编写一个函数来分发任务,在多线程中执行map_reduce
    print(execute(works))


def _main():
    # 从一个目录中读取文件并统计行数
    # 创建一些模拟文件
    temp_dir = Path('./temp_dir')
    temp_dir.mkdir(exist_ok=True)
    for i in range(10):
        with open(f'./temp_dir/file_{i}.txt', 'w') as f:
            f.write('\n' * random.randint(0, 100))  # 写入行换行符

    map_reduce(temp_dir)
```

- 这种做法目前是可用的,当需要拓展的时候就有问题了
- 原因是在函数 build_input_data creat_works 中已经指定了具体的类型,
- 这个问题的根本原因在于，**构造对象的办法不够通用**
- 在其他编程语言中，可以利用构造函数多态,提供一个特殊的构造函数以实现和自身有关的构造逻辑
- 就可以按照超类的形式统一地构造这些对象，
- 并使其根据所属的子类分别去触发相关的特殊构造函数（类似工厂模式)

-改进

- 在超类中定义一个@classmethod方法，用来构造子类对象

```python
class InputData:
    """输入数据的超类"""

    @abc.abstractmethod
    def read(self) -> 'str':
        """读取输入数据"""
        ...

    @classmethod
    def generate_inputs(cls, config) -> 'Iterator[InputData]':
        ...

```

- 在子类中实现这个方法

```python
class PathInputData(InputData):
    def __init__(self, path: str):
        self.path = path

    def read(self) -> 'str':
        with open(self.path, 'r') as _f:
            return _f.read()

    @classmethod
    def generate_inputs(cls, config) -> 'Iterator[PathInputData]':
        data_dir = Path(config['data_dir'])
        for i in os.listdir(data_dir):
            yield cls(os.path.join(data_dir, i))

```

- Work类也做类似的改动

```python
class Worker:
    def __init__(self, input_data: 'InputData'):
        self.input_data = input_data
        self.result = 0

    @abc.abstractmethod
    def map(self):
        pass

    @abc.abstractmethod
    def reduce(self, other):
        pass

    @classmethod
    @abc.abstractmethod
    def generate_workers(cls, inputs: 'Iterator[InputData]'):
        ...


class LineCountWorker(Worker):
    """统计一个文件中换行符的个数"""

    def __init__(self, input_data):
        super().__init__(input_data)

    def map(self):
        data = self.input_data.read()
        self.result = data.count('\n')

    def reduce(self, other: 'Worker'):
        return self.result + other.result

    @classmethod
    def generate_workers(cls, inputs: 'List[InputData]'):
        return [cls(i) for i in inputs]

```

- 最后改写一下应用函数

```python
def map_reduce2(work_cls: 'Type[Worker]',
                input_data_cls: 'Type[InputData]',
                temp_dir: 'Path'):
    config = {'data_dir': temp_dir}
    input_datas = input_data_cls.generate_inputs(config)
    works = work_cls.generate_workers(input_datas)
    print(execute(works))


map_reduce2(LineCountWorker, PathInputData, temp_dir)
```

- 这样改进后,当使用不同的类来处理的时候,入参指明类型即可

## 第40条　通过super初始化超类

- Python内置了super函数并且规定了标准的方法解析顺序
- 主要是解决面向多继承和菱形继承带来的一些问题
- 略

## 第41条　考虑用mix-in类来表示可组合的功能

- 应该尽量少用多重继承
- 如果既要通过多重继承来方便地封装逻辑，
- 又想避开可能出现的问题，那么就应该把有待继承的类写成mix-in类
- 这种类只提供一小套方法给子类去沿用，而不定义自己实例级别的属性
- 也不需要__init__构造函数。
- 有点类似于实现或者继承一个接口
- 如果子类要定制（或者说修改）mix-in所提供的功能，
- 那么可以在自己的代码里面覆盖相关的实例方法

## 第42条　优先考虑用public属性表示应受保护的数据，不要用private属性表示

- Python类的属性只有两种访问级别，也就是public与private
- 双下划线开头的属性即为实例的private属性
- private字段只给这个类自己使用，子类不能访问超类的private字段
- 这种防止其他类访问private属性的功能，其实仅仅是通过变换属性名称而实现的
- python解释器是做了名称变换 self.__a >>> self._ClassName__a

## 第43条　自定义的容器类型应该从collections.abc继承

- 如果要编写的新类比较简单，那么可以直接从Python的容器类型（例如list或dict）里面继承。
- 如果想让定制的容器类型能像标准的Python容器那样使用，那么有可能要编写许多特殊方法。

- 可以从collections.abc模块里的抽象基类之中派生自己的容器类型，
- 这样可以让容器自动具备相关的功能，同时又可以保证没有把实现这些功能所必备的方法给漏掉

# 元类与属性

元类能够拦截Python的class语句，让系统每次定义类的时候，都能实现某些特殊的行为

## 第44条　用纯属性取代get和set方法

- 定义属性时 应该从最简单的public属性开始写起
- 将来如果想在设置属性时，实现特别的功能
- 可以先通过@property修饰器来封装获取属性的那个方法
- 并在封装出来的修饰器上面通过setter属性来封装设置属性的那个方法

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        # self.age = age
        self._age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Age cannot be negative")
        self._age = value


if __name__ == '__main__':
    # 这种不能阻止在构造的时候校验
    p = Person("John", -5)
    print(p.age)

```

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        # self.age = age
        self._age = self._check_age(age)

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        self._age = self._check_age(value)

    @staticmethod
    def _check_age(value):
        if value < 0:
            raise ValueError("Age cannot be negative")
        return value


if __name__ == '__main__':
    # 可以把校验逻辑封装一下,最开始构建的时候也同样检查
    p = Person("John", -5)
    print(p.age)

```

- 实现@property方法时，应该遵循最小惊讶原则，不要引发奇怪的副作用
- @property方法必须执行得很快。
- 复杂或缓慢的任务，尤其是涉及I/O或者会引发副作用的那些任务，还是用普通的方法来实现比较好
- @property可以帮助解决实际工作中的许多问题，但不应该遭到滥用
- 如果开发过程中总是在写复杂的property 说明需要重构

## 第46条　用描述符来改写需要复用的@property方法

- 描述符
    * python描述符是一个“绑定行为”的对象属性，在描述符协议中，它可以通过方法重写属性的访问。
    * 这些方法有 __get__(), __set__(), 和__delete__()。
    * __ get__(self, instance, owner)
    * __ set__(self, instance, value)
    * __ delete__(self, instance)
    * 如果这些方法中的任何一个被定义在一个对象中，
    * 这个对象就是一个描述符。
    *
- 对象的__dict__ 属性
    * 每个对象都有一个__dict__属性，它是一个字典，存储了该对象的所有属性。

```python
class Grade:
    def __get__(self, instance, owner):
        ...

    def __set__(self, instance, value):
        ...


class Exam:
    math_grade = Grade()
    english_grade = Grade()


if __name__ == '__main__':
    exam = Exam()
    exam.math_grade = 90
    print(exam.math_grade)  # None
# 因为math_grade是一个Grade类的实例，而不是一个属性,而此时Grade.__get__ 还没实现
# 返回None
```

- 执行 exam.math_grade 点操作时
- python解释器是调用exam.__ dict__['math_grade']

```python
class Grade:
    def __get__(self, instance, owner):
        ...

    def __set__(self, instance, value):
        ...


class Exam:
    math_grade = Grade()
    english_grade = Grade()
    english_grade3 = 0

    def __init__(self):
        self.math_grade = 0
        self.english_grade2 = 0


if __name__ == '__main__':
    exam = Exam()
    print(exam.__dict__)
    print(Exam.__dict__)

"""
{'english_grade2': 0}

{
    '__module__': '__main__',
    'math_grade': <__main__.Grade object at 0x0000015DE525A850>,
    'english_grade': <__main__.Grade object at 0x0000015DE525A9A0>,
    'english_grade3': 0,
     '__init__': <function Exam.__init__ at 0x0000015DE5427E50>,
    '__dict__': <attribute '__dict__' of 'Exam' objects>,
    '__weakref__': <attribute '__weakref__' of 'Exam' objects>, '__doc__': None
}
"""

```

- 实例的字典中只有仅在实例中定义的字段 english_grade2
- 当属性在实例中有类中定义的同名字段的时候 math_grade 此时访问实例属性的时候,会转而访问类中字段
- 并且同一个类中的字段会被这个类的各个实例所共享

- 当属性在类的字段中且这个字段是一个描述符字段,
  那么访问和修改时就会触发__ get__ 和__ set__ 方法

```python
class Grade:
    def __get__(self, instance, owner):
        print('↓' * 30)
        print(f'__get__(self={self}, instance={instance}, owner={owner})')
        print('↑' * 30)
        return 100

    def __set__(self, instance, value):
        print('↓' * 30)
        print(f'__set__(self={self}, instance={instance}, value={value})')
        print('什么也不做')
        print('↑' * 30)


class Exam:
    math_grade = Grade()
    english_grade = Grade()
    english_grade3 = 0

    def __init__(self):
        print(222222222)
        self.math_grade = 0  # 触发__set__
        print(33333333)
        self.english_grade2 = 0  # 不触发__set__


if __name__ == '__main__':
    print(11111111)
    exam = Exam()
    print(444444444)
    exam.math_grade = 50

    print(exam.math_grade)  # 触发__get__ 并且返回100

    print(exam.__dict__)
    print(Exam.__dict__)



```

```python
# __set__(
# self=<__main__.Grade object at 0x000001A9F7EAA850>,
# instance=<__main__.Exam object at 0x000001A9F7EAAA90>, 
# value=50
# )

# __get__(
# self=<__main__.Grade object at 0x000001A9F7EAA850>,
# instance=<__main__.Exam object at 0x000001A9F7EAAA90>,
# owner=<class '__main__.Exam'>
# )

# 观察这几个参数的信息
# self 是描述符对象本身
# instance 是实例对象
# owner 是instance 的 类对象
# value 是 当进行赋值操作的时候 提供的值

```

- 因为描述符是定义在类对象上的,所以当实例来进行访问的时候,而且可以用于多个类
- 所以 instance owner 的传入才可以给解释器提供足够明确的信息
- 如果不加区分,那就是所有描述符都是全局共享的
- 所以在可以描述符内部维护一个字典,来保存每个实例具体应该获得的值

````python
class Grade:
    def __init__(self):
        self._values = {}

    def __get__(self, instance, owner):
        return self._values.get(instance, 60)

    def __set__(self, instance, value):
        if value < 0:
            raise ValueError('grade must be positive')
        self._values[instance] = value


class Exam:
    math_grade = Grade()
    english_grade = Grade()
    english_grade3 = 0

    def __init__(self):
        print(222222222)
        self.math_grade = 0  # 触发__set__
        print(33333333)
        self.english_grade2 = 0  # 不触发__set__


if __name__ == '__main__':
    print(11111111)
    exam = Exam()
    print(444444444)

    # exam.math_grade = -50
    exam.math_grade = 50
    exam.english_grade = 150

    print(exam.math_grade)  # 触发__get__
    print(exam.english_grade)  # 触发__get__

    print(exam.__dict__)
    print(Exam.__dict__)

````

- 还有一个缺点,就是直接使用dict来保存会导致内存泄漏
- 因此真正的做法是使用WeakKeyDictionary 弱引用字典

```python
from weakref import WeakKeyDictionary


class Grade:
    def __init__(self):
        self._values = WeakKeyDictionary()

    def __get__(self, instance, owner):
        print(self._values)
        return self._values.get(instance, 60)

    def __set__(self, instance, value):
        if value < 0:
            raise ValueError('grade must be positive')
        self._values[instance] = value


class Exam:
    math_grade = Grade()
    english_grade = Grade()
    english_grade3 = 0

    def __init__(self):
        print(222222222)
        self.math_grade = 0  # 触发__set__
        print(33333333)
        self.english_grade2 = 0  # 不触发__set__


if __name__ == '__main__':
    print(11111111)
    exam = Exam()
    print(444444444)

    # exam.math_grade = -50
    exam.math_grade = 50
    exam.english_grade = 150

    print(exam.math_grade)  # 触发__get__
    print(exam.english_grade)  # 触发__get__

    print(exam.__dict__)
    print(Exam.__dict__)

```

- 另外还以一个__delete__方法,用于删除实例的属性,也属于描述符魔术方法,用法和上面类似

## 第47条　针对惰性属性使用__getattr__、__getattribute__及__setattr__

- 当访问对象的摸一个属性不存在的时候,会触发__getattr__方法

```python
class Foo:
    def __init__(self):
        self.x = 0
        self.y = 0

    def __getattr__(self, item):
        print(f'item={item}')
        return


if __name__ == '__main__':
    foo = Foo()
    print(foo.z)
    """
    item=z
    None
    """

```

- __ getattribute__ 方法,不管属性存不存在,只要是访问属性都会触发
- __ setattr__ 当给属性赋值的时候,不管是直接赋值还是通过内置的setattr()方法

## 第48条　用__init_subclass__验证子类写得是否正确

- 什么是元类
- class定义的类对象指导创建实例对象
- 而元类指导解释器创建类对象
- Python中万物皆对象，一个Python对象可能拥有两个属性，
- __ class__ 和 __ bases__，
- __class__表示这个对象是谁创建的，
- __bases__表示这个类的父类。
- __class__和type()函数效果一样。

```python
class A:
    def __init__(self):
        self.x = 0


class B(A):
    def __init__(self):
        super().__init__()


b = B()

print(B.__bases__)  # (<class '__main__.A'>,)
print(A.__bases__)  # (<class 'object'>,)
print(b.__class__)  # <class '__main__.B'>

```

- 继承关系说明了谁是谁的实例或者子类
- type为对象的顶点，所有对象都创建自type。
  object为类继承的顶点，所有类都继承自object。
- object是所有类的超类，而且type也是继承自object；
  所有对象创建自type，而且object也是type的实例。
- 就像str是用来创建字符串对象的类，int是用来创建整数对象的类，而type就是创建类对象的类。
- 类定义class语句中不写metaclass=xxx,解释器默认就是使用type本身作为元类
- 自定义的元类应该继承type

---

- type如何创建类

```python
"""来自builtins.py type.__init__ 文档
        type(object) -> the object's type
        type(name, bases, dict) -> a new type
"""
# builtins.pyi 注解文件
# @overload
# def __init__(self, o: object) -> None: ...
# @overload
# def __init__(self, name: str, bases: Tuple[type, ...], dict: dict[str, Any], **kwds: Any) -> None: ...
```

- type有两种基础用法

* type(object) 获得这个object的class 相当于调用object.__ class__

```python
class A:
    pass


a = A()
print(a.__class__)  # <class '__main__.A'>
print(type(a))  # <class '__main__.A'>
print(A.__class__)  # <class 'type'>
print(type(A))  # <class 'type'>


```

* type(name, bases, dict) 返回一个名字是name 的类,指定他的父类,和字段

```python
class A:
    a = 'a'
    pass


B = type('B', (A,), {'b': 'b', 'b_print': lambda x: print(x.b)})
b = B()
print(b.__repr__())
# <__main__.B object at 0x0000021EF611B7F0>  说明b确实是B的实例
b.b_print()  # b 说明字典中的属性被填入到了B中,
print(b.a)  # a 继承自 A 所有也有a属性
print(B.__bases__)  # (<class '__main__.A'>,) 更直观的说明B是A的子类
```

- type(name, bases, dict) 实际上是调用type.__ new__(cls,name,bases, dict)

```python
# - type(name, bases, dict) 实际上是调用type.__ new__(cls,name,bases, dict)
class Meta(type):
    def __new__(cls, name, bases, dicts):
        print(222)
        res = super().__new__(cls, name, bases, dicts)
        print(res)  # <class '__main__.A'>
        print(333)
        return res


class A(metaclass=Meta):
    print(111)
    ...

```

- 当我们使用a=A()这种方法创建对象的时候,实际上是type.__ call__()创建对象,
  然后a.__ init__()初始化

```python
class Meta(type):
    def __call__(cls, *args, **kwargs):
        print('123')
        res = super().__call__(*args, **kwargs)  # 这里就会去调用具体的__init__
        print(res)  # <__main__.A object at 0x000002045FACA460>
        return res


class A(metaclass=Meta):
    def __init__(self, a):
        print('456')
        self.a = a
        print(a)


class B(metaclass=type):
    def __init__(self, a):
        print('456')
        self.a = a
        print(a)


a = A('789')
b = B(101)
print(a.__class__.__class__)  # <class '__main__.Meta'>
print(b.__class__.__class__)  # <class 'type'>
# 创建a的时候会打印123而b不会

```

```python
#  结合__new__ 和 __call__ 就可以定义一个拥有和原生type不同特性的类了
class Meta(type):
    _instances = None

    def __new__(cls, name: str, bases, dicts):
        if name.startswith('Single'):
            print('我是一个单例对象,')
            dicts['isSingle'] = True
        else:
            print('我是不是一个单例对象,')
            name = 'NotSingle' + name
            dicts['isSingle'] = False
        return super().__new__(cls, name, bases, dicts)

    def __call__(cls, *args, **kwargs):
        if cls.isSingle:
            if not cls._instances:
                cls._instances = super().__call__(*args, **kwargs)
                return cls._instances
            else:
                return cls._instances
        return super().__call__(*args, **kwargs)


class SingleA(metaclass=Meta):
    def __init__(self):
        self.a = 0


class A(metaclass=Meta):
    def __init__(self):
        self.a = 0


a1 = SingleA()
a2 = SingleA()
a3 = A()
a4 = A()
print(id(a1), a1.__class__.__name__)
print(id(a2), a2.__class__.__name__)
print(id(a3), a3.__class__.__name__)
print(id(a4), a4.__class__.__name__)
# 2187437389808 SingleA
# 2187437389808 SingleA
# 2187492544768 NotSingleA
# 2187492544816 NotSingleA

```

- 总之,只要class语句中当前类或者他的父类定义了metaclass,就会使用metaclass来代替原生的type
- 至于这个metaclass可以是继承了type的类(常用做法),
- 也可以是别的什么东西,只要metaclass是一个接受name, bases, dicts 并且返回一个类型的对象

```python
"""用函数实现的代替"""


class A:
    cls_a = 1

    def __init__(self):
        self.a = 2


def meta_fun(name: str, bases, dicts):
    return A


class B(metaclass=meta_fun):
    ...


a = B()
print(a.__class__)

```

---

- 用元类来验证子类写的正不正确

```python
# 定义一个多边形基类
class Polygon:
    edge: int  # 边数


# 三角形继承基类,并且定义边数为3
class Triangle(Polygon):
    edge = 3


# 但是有可能我们因为疏忽会写出边数为2的多边形
# 这样的语句解释器是不知道有错误的,后面的应用中可能会错误的使用这个类
# 或者我们 只提供了基类,使用者基于基类构建自己的多边形类,然而定义的过程不受控制,
# 有错误也无法得知
class Triangle2(Polygon):
    edge = 2

```

```python
class MetaPolygon(type):
    def __new__(cls, name, bases, clas_dict):
        # 基类不需要验证
        if name != 'Polygon':
            if clas_dict['edge'] <= 2:
                raise ValueError('边数必须大于等于3')
        return super().__new__(cls, name, bases, clas_dict)


# 定义一个多边形基类
class Polygon(metaclass=MetaPolygon):
    edge: 'int'


# 三角形继承基类,并且定义边数为3
class Triangle(Polygon):
    edge = 3


class Triangle2(Polygon):  # 运行到这里会报错,无法编写这样的类,程序不能启动
    edge = 2
```

- 用元类来实现这种功能非常灵活,可以完成有许多任务,但是也有缺点
- 一个类只能有一个元类,当我们有另一个元类处理别的功能的时候,要想实现两个功能都具备
  就需要把两份完全不相干的代码组合在一起,导致耦合度增大,不利于拓展和复用,
  或者是定义一个元类体系,每一层元类做一件事,层级太多的话会导致代码难以阅读和追踪
- 元类的写法本身就比较不直观,不熟悉元类的难以读懂

- 上述功能可以使用__init_subclass__功能实现
- 对于一个类，如果这个类被作为父类继承，解释器运行class语句时
- 会触发其内部的__init_subclass__方法

```python
class Base:
    def __init_subclass__(cls, *args, **kwargs):
        print(f'__init_subclass__({cls}, *{args},**{kwargs})')


# __init_subclass__(<class '__main__.A'>, *(),**{'name': '张三', 'age': 18})
# 定义类的时候是可以写一些其他kwargs的,可以被__init_subclass__捕获
class A(Base, name='张三', age=18):
    pass

```

- 可以非常简单的实现验证逻辑,而且很好理解

```python
# 定义一个多边形类
class Polygon:
    edge = 0

    def __init_subclass__(cls, **kwargs):
        super().__init_subclass__()  # 必须要加这一句,才能正确处理多继承和多级继承
        if cls.edge <= 2:
            raise ValueError(f'{cls}边数必须大于2')


class Triangle(Polygon):
    edge = 2

```

- 复用多个逻辑

```python
# 定义一个多边形类
class Polygon:
    edge = 0

    def __init_subclass__(cls, **kwargs):
        super().__init_subclass__()
        if cls.edge <= 2:
            raise ValueError(f'{cls}边数必须大于2')


class LineType:
    line_type = '直线'

    def __init_subclass__(cls, **kwargs):
        super().__init_subclass__()
        if cls.line_type not in ['直线', '虚线']:
            raise ValueError(f'{cls}线型必须是直线或虚线')


class Triangle(Polygon, LineType):
    edge = 3
    line_type = None
```

-通过super()来调用超类的__init_subclass__方法，以便按照正确的顺序触发各类的验证逻辑

## 第49条   通过super()来调用超类的__init_subclass__方法，以便按照正确的顺序触发各类的验证逻辑

- 一个常用的场景是我们需要把某些类型缓存下来,以便后续使用

```python
# 把一个类型转成json串
# 把一个json串转成python对象
import json


class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def to_json(self):
        ...

    @classmethod
    def from_json(cls, json_str):
        ...


class Teacher(Person):
    def __init__(self, name, age, subject):
        super().__init__(name, age)
        self.subject = subject

    def to_json(self):
        return json.dumps({
            'class_name': 'Teacher',
            'name': self.name,
            'age': self.age,
            'subject': self.subject,
        })

    @classmethod
    def from_json(cls, json_str):
        dict_ = json.loads(json_str)
        return cls(dict_['name'], dict_['age'], dict_['subject'], )


class Student(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self.grade = grade

    def to_json(self):
        return json.dumps({
            'class_name': 'Student',
            'name': self.name,
            'age': self.age,
            'grade': self.grade,
        })

    @classmethod
    def from_json(cls, json_str):
        dict_ = json.loads(json_str)
        return cls(dict_['name'], dict_['age'], dict_['grade'], )


t1 = Teacher('张老师', 28, 'math')
s1 = Student('李同学', '18', 85)
t1_str = t1.to_json()
s1_str = s1.to_json()
print(t1_str)
print(s1_str)
t2 = Teacher.from_json(t1_str)
s2 = Student.from_json(s1_str)
print(t2.to_json())
print(s2.to_json())

```

- 通过类方法来生成类对象
- 这个时候得编码者提前知道这个字符串是属于哪个类型的

```python
class_dict = {
    'Student': Student,
    'Teacher': Teacher,
}
data = json.loads(t1_str)
cls_name = data.pop('class_name')
cls = class_dict[cls_name]
print(cls.from_json(t1_str))

```

- 要使用类似这种方式以实现自动选择类型,一旦这个字典忘记更新新的类型进去,就会出错

---

- 利用元类来实现自动更新

```python
# 把一个类型转成json串
# 把一个json串转成python对象
# 利用元类实现自动注册类型
import json

class_name = {}


class MetaAutoRegister(type):
    def __new__(cls, name, bases, attr_dict):
        if name not in class_name:
            class_name[name] = super().__new__(cls, name, bases, attr_dict)
        return class_name[name]


class Person(metaclass=MetaAutoRegister):
    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def tojson(cls):
        ...

    @classmethod
    def from_json(cls, json_str):
        ...


class Teacher(Person):
    def __init__(self, name, age, subject):
        super().__init__(name, age)
        self.subject = subject

    def tojson(self):
        return json.dumps({
            'class_name': 'Teacher',
            'name': self.name,
            'age': self.age,
            'subject': self.subject,
        })

    @classmethod
    def from_json(cls, json_str):
        dict_ = json.loads(json_str)
        return cls(dict_['name'], dict_['age'], dict_['subject'], )


class Student(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self.grade = grade

    def tojson(self):
        return json.dumps({
            'class_name': 'Student',
            'name': self.name,
            'age': self.age,
            'grade': self.grade,
        })

    @classmethod
    def from_json(cls, json_str):
        dict_ = json.loads(json_str)
        return cls(dict_['name'], dict_['age'], dict_['grade'], )


print(class_name)  # 实现了自动注册类型
# {'Person': <class '__main__.Person'>,
# 'Teacher': <class '__main__.Teacher'>,
# 'Student': <class '__main__.Student'>}
t1 = Teacher('张老师', 28, 'math')
t1_str = t1.tojson()
data = json.loads(t1_str)
cls_name = data.pop('class_name')
cls = class_name[cls_name]
obj = cls.from_json(t1_str)
print(obj)

```

---

- 和检验案例一样,可以不使用元类,而是用init_subclass 更简单的实现

```python
# 把一个类型转成json串
# 把一个json串转成python对象
# 利用元类实现自动注册类型
import json

class_name = {}


class AutoRegistration:
    def __init_subclass__(cls, **kwargs):
        class_name[cls.__name__] = cls
        super().__init_subclass__(**kwargs)


# 甚至可以不写这个自动注册类,因为目前只有Person及子类需要注册
# 需要拓展的时候再写也可以

class Person(AutoRegistration):

    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def tojson(cls):
        ...

    @classmethod
    def from_json(cls, json_str):
        ...


class Teacher(Person):
    def __init__(self, name, age, subject):
        super().__init__(name, age)
        self.subject = subject

    def tojson(self):
        return json.dumps({
            'class_name': 'Teacher',
            'name': self.name,
            'age': self.age,
            'subject': self.subject,
        })

    @classmethod
    def from_json(cls, json_str):
        dict_ = json.loads(json_str)
        return cls(dict_['name'], dict_['age'], dict_['subject'], )


class Student(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self.grade = grade

    def tojson(self):
        return json.dumps({
            'class_name': 'Student',
            'name': self.name,
            'age': self.age,
            'grade': self.grade,
        })

    @classmethod
    def from_json(cls, json_str):
        dict_ = json.loads(json_str)
        return cls(dict_['name'], dict_['age'], dict_['grade'], )


print(class_name)  # 实现了自动注册类型
# {'Person': <class '__main__.Person'>,
# 'Teacher': <class '__main__.Teacher'>,
# 'Student': <class '__main__.Student'>}
t1 = Teacher('张老师', 28, 'math')
t1_str = t1.tojson()
data = json.loads(t1_str)
cls_name = data.pop('class_name')
cls = class_name[cls_name]
obj = cls.from_json(t1_str)
print(obj)

```

## 第50条　用__set_name__给类属性加注解

- 元类还有一个更有用的功能，那就是可以在某个类真正投入使用之前，率先修改或注解这个类所定义的属性
- 这通常需要与描述符（descriptor）搭配使用
- 普通写法

```python
# 定义一个类 用于表述数据库中某个表的一行

# 先定义一个描述符,标示列名及数据

# 这里直接给属性复制,比前面提到的用弱引用字典保存要直接
class Filed:
    def __init__(self, name):
        self.name = name  # 列名 用于Customer类属性
        self.internal_name = '_' + name  # 用于customer实例属性

    def __get__(self, instance, owner):
        if instance is None:
            return None
        return getattr(instance, self.internal_name, '')

    def __set__(self, instance, value):
        setattr(instance, self.internal_name, value)  # 给列名赋值


# 定义一个Customer表示一行数据,
# 共4列数据
class Customer:
    first_name = Filed('first_name')
    last_name = Filed('last_name')
    prefix = Filed('prefix')
    suffix = Filed('suffix')


customer = Customer()
customer2 = Customer()

customer.last_name = 'L'
customer.first_name = 'M'
print(customer._last_name)
print(customer._first_name)

customer2.last_name = 'K'
customer2.first_name = 'N'
print(customer2._last_name)
print(customer2._first_name)

```

- first_name = Filed('first_name') 这条语句重复的输入列名
- 利用元类来消除这种重复

```python
class Filed:
    def __init__(self):
        self.name = None  # 列名 用于Customer类属性
        self.internal_name = None  # 用于customer实例属性

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return getattr(instance, self.internal_name, '')

    def __set__(self, instance, value):
        setattr(instance, self.internal_name, value)  # 给列名赋值


class MetaCustom(type):
    def __new__(cls, name, bases, class_dict):
        for k, v in class_dict.items():
            if isinstance(v, Filed):
                v.name = k
                v.internal_name = '_' + k
        return super().__new__(cls, name, bases, class_dict)


class Customer(metaclass=MetaCustom):
    first_name = Filed()
    last_name = Filed()
    prefix = Filed()
    suffix = Filed()


customer = Customer()
customer2 = Customer()

customer.last_name = 'L'
customer.first_name = 'M'
print(customer._last_name)
print(customer._first_name)

customer2.last_name = 'K'
customer2.first_name = 'N'
print(customer2._last_name)
print(customer2._first_name)


```

- 可以省去元类的写法,直接在描述符中使用__set_name__

```python
class Filed:
    def __init__(self):
        self.name = None  # 列名 用于Customer类属性
        self.internal_name = None  # 用于customer实例属性

    def __get__(self, instance, owner):
        if instance is None:
            return self
        return getattr(instance, self.internal_name, '')

    def __set__(self, instance, value):
        setattr(instance, self.internal_name, value)  # 给列名赋值

    def __set_name__(self, owner, name):
        # 调用时机在class语句
        # 当解释器发现一个类的字段是描述符时 会触发
        # name 就是字段的名称
        # owner 就是那个类
        self.name = name
        self.internal_name = '_' + name


class Customer():
    first_name = Filed()
    last_name = Filed()
    prefix = Filed()
    suffix = Filed()


customer = Customer()
customer2 = Customer()

# 现在类里有4个字段都是Filed描述符,
# 这个Filed描述符实例里保存着两个名字
print(customer.__dict__)
customer.last_name = 'L'
# 在这里调用__set__ 于是customer实例中有了一个_开头的属性
customer.first_name = 'M'
print(customer.__dict__)
print(customer._last_name)
# 调用__get__
print(customer._first_name)

customer2.last_name = 'K'
customer2.first_name = 'N'
print(customer2._last_name)
print(customer2._first_name)

```

## 第51条　优先考虑通过类修饰器来提供可组合的扩充功能，不要使用元类

- 一种常见的功能是我们写的某个类,及它的子类,将会提供若干个方法,
  希望这些方法除了在做本身的任务之外,还能额外的做一些工作,比如记录调试参数信息到日志中
- 通常会用装饰器来实现这样的工作

```python
import collections
import functools


def log_time(func):
    functools.wraps(func)
    count = collections.defaultdict(lambda: 0)

    def inner(*args, **kwargs):
        nonlocal count
        flag = str((args[0], func.__name__))
        count[flag] += 1
        print(f'函数{flag}第{count[flag]}次调用')
        return func(*args, **kwargs)

    return inner


class Foo:
    @log_time
    def __init__(self, value):
        self.value = value

    @log_time
    def print_self(self):
        print(self.value)


f = Foo(3)
f.print_self()
f.print_self()
f2 = Foo(4)
f2.print_self()
f.print_self()
# 函数(<__main__.Foo object at 0x000001A9892BA9A0>, '__init__')第1次调用
# 函数(<__main__.Foo object at 0x000001A9892BA9A0>, 'print_self')第1次调用
# 3
# 函数(<__main__.Foo object at 0x000001A9892BA9A0>, 'print_self')第2次调用
# 3
# 函数(<__main__.Foo object at 0x000001A989481BB0>, '__init__')第1次调用
# 函数(<__main__.Foo object at 0x000001A989481BB0>, 'print_self')第1次调用
# 4
# 函数(<__main__.Foo object at 0x000001A9892BA9A0>, 'print_self')第3次调用
# 3

```

- 当我们拓展Foo类的时候 被重写的方法将不会被记录

```python
import collections
import functools


def log_time(func):
    functools.wraps(func)
    count = collections.defaultdict(lambda: 0)

    def inner(*args, **kwargs):
        nonlocal count
        flag = str((args[0], func.__name__))
        count[flag] += 1
        print(f'函数{flag}第{count[flag]}次调用')
        return func(*args, **kwargs)

    return inner


class Foo:
    @log_time
    def __init__(self, value):
        self.value = value

    @log_time
    def print_self(self):
        print(self.value)


class Foo2(Foo):
    ...

    def print_self(self):
        print(float(self.value))


f = Foo2(3)
f.print_self()
f.print_self()
f2 = Foo2(4)
f2.print_self()
f.print_self()
# 函数(<__main__.Foo2 object at 0x0000019CB2BEAE80>, '__init__')第1次调用
# 3.0
# 3.0
# 函数(<__main__.Foo2 object at 0x0000019CB6115100>, '__init__')第1次调用
# 4.0
# 3.0
```

- 可以使用元类,在定义类的时候,自动的把方法包装起来

```python
import collections
import functools
from typing import Callable


def log_time(func):
    functools.wraps(func)
    count = collections.defaultdict(lambda: 0)

    def inner(*args, **kwargs):
        nonlocal count
        flag = str((args[0], func.__name__))
        count[flag] += 1
        print(f'函数{flag}第{count[flag]}次调用')
        return func(*args, **kwargs)

    return inner


class MetaFoo(type):
    def __new__(cls, name, bases, class_dict):
        for k, v in class_dict.items():
            if isinstance(v, Callable):
                class_dict[k] = log_time(v)
        return super().__new__(cls, name, bases, class_dict)


class Foo(metaclass=MetaFoo):
    def __init__(self, value):
        self.value = value

    def print_self(self):
        print(self.value)


class Foo2(Foo):
    ...

    def print_self(self):
        print(float(self.value))


f = Foo2(3)
f.print_self()
f.print_self()
f2 = Foo2(4)
f2.print_self()
f.print_self()
# 函数(<__main__.Foo2 object at 0x000001E5B78B5220>, '__init__')第1次调用
# 函数(<__main__.Foo2 object at 0x000001E5B78B5220>, 'print_self')第1次调用
# 3.0
# 函数(<__main__.Foo2 object at 0x000001E5B78B5220>, 'print_self')第2次调用
# 3.0
# 函数(<__main__.Foo2 object at 0x000001E5B797E2B0>, '__init__')第1次调用
# 函数(<__main__.Foo2 object at 0x000001E5B797E2B0>, 'print_self')第1次调用
# 4.0
# 函数(<__main__.Foo2 object at 0x000001E5B78B5220>, 'print_self')第3次调用
# 3.0

```

- 只要继承自Foo类的任何子类,都可以获得这一特性(方法将被日志记录)
- 和之前提到过的缺点一样,不同特性元类不方便组合,元类比较难懂
- 可以使用类装饰器的方法来实现
- 写法和函数装饰器非常类似

```python
# 这是两种错误的写法,不能直接这样修改__dict__字典 
# 在元类方法的时候,这个类还没创建这个dict还是一个普通字典 作为创建类type的参数使用的
# 装饰器方法的时候,这个类已经创建好了,这个__dict__属性已经不可直接写了

# def add_log_time(klass):
#     old_dict = dict(klass.__dict__)
#     for k, v in old_dict.items():
#         if isinstance(v, Callable):
#             old_dict[k] = log_time(v)
#     klass.__dict__ = old_dict
#     return klass
"AttributeError: attribute '__dict__' of 'type' objects is not writable"

# def add_log_time(klass):
#     for k, v in klass.__dict__.items():
#         if isinstance(v, Callable):
#             klass.__dict__[k] = log_time(v)
#     return klass
"TypeError: 'mappingproxy' object does not support item assignment"



```

```python
import collections
import functools
from typing import Callable


def log_time(func):
    functools.wraps(func)
    count = collections.defaultdict(lambda: 0)

    def inner(*args, **kwargs):
        nonlocal count
        flag = str((args[0], func.__name__))
        count[flag] += 1
        print(f'函数{flag}第{count[flag]}次调用')
        return func(*args, **kwargs)

    return inner


def add_log_time(klass):
    for k, v in klass.__dict__.items():
        if isinstance(v, Callable):
            setattr(klass, k, log_time(v))
    return klass


def spilt_log(func):
    def inner(*args, **kwargs):
        print('↓' * 50)
        res = func(*args, **kwargs)
        print('↑' * 50 + '\n' * 3)
        return res

    return inner


def add_spilt_log(klass):
    for k, v in klass.__dict__.items():
        if isinstance(v, Callable):
            setattr(klass, k, spilt_log(v))
    return klass


@add_log_time
class Foo:
    def __init__(self, value):
        self.value = value

    def print_self(self):
        print(self.value)


@add_log_time
@add_spilt_log
class Foo2(Foo):
    ...

    def print_self(self):
        print(float(self.value))


f = Foo2(3)
f.print_self()
f.print_self()
f2 = Foo2(4)
f2.print_self()
f.print_self()
# 函数(<__main__.Foo2 object at 0x00000204DB6DE3A0>, '__init__')第1次调用
# 函数(<__main__.Foo2 object at 0x00000204DB6DE3A0>, 'inner')第1次调用
# ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
# 3.0
# ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
# 
# 
# 
# 函数(<__main__.Foo2 object at 0x00000204DB6DE3A0>, 'inner')第2次调用
# ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
# 3.0
# ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
# 
# 
# 
# 函数(<__main__.Foo2 object at 0x00000204DB6DE0D0>, '__init__')第1次调用
# 函数(<__main__.Foo2 object at 0x00000204DB6DE0D0>, 'inner')第1次调用
# ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
# 4.0
# ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

```

- 使用类装饰器子类不会自动继承父类的装饰,但是比起每个方法都要写还是要简单
- 然后他是可以实现多个装饰器叠加使用的

# 第7章　并发与并行

## 第52条　用subprocess管理子进程

- Python解释器本身有可能会局限在一个CPU上面(GIL) 使用子进程可以拓展
- Python里面有许多方式都可以运行子进程（例如os.popen函数以及os.exec*系列的函数)
- 其中最好的办法是通过内置的subprocess模块来管理
- subprocess.run() 执行一条cmd命令 能不能执行取决于系统的cmd命令行有没有相应的命令

```python
import subprocess

cmd = 'python --version'
subprocess.run(cmd, shell=True)
subprocess.call(cmd, shell=True)

subprocess.call(['python', '--version'], )

```

```python
import subprocess

# 传入一条命令 并且捕获命令的输出
cmd = 'python --version'
res = subprocess.run(cmd, shell=True, capture_output=True, encoding='utf-8')
# res是一个 subprocess.CompletedProcess 实例,如果命令正常结束可以通过下面的方法
res.check_returncode()
# 正常输出在stdout中
print(res.stdout)

```

- 通过Popen后台启动子进程

```python
import subprocess

cmd = 'python -c "import time;time.sleep(1);exit({})"'.format(0)
proc = subprocess.Popen(cmd, shell=True)  # 启动一个命令行子进程
while (status := proc.poll()) is None:  # 在父进程中查询子进程的状态
    print(status)
    # 当子进程还没有结束的时候,status是None
    # 对于python 返回值是exit(code)
    # 默认约定返回零时表示正常退出
    print('1')

print(f'exit,{status}')

```

- 可以平行的执行多个子进程
- 先写一个脚本,暂停一秒然后写一个当前进程id名的文件

```python
# test.py
import multiprocessing
import time

time.sleep(1)

f_name = multiprocessing.process.current_process().pid
with open(f'{f_name}.txt', 'w') as f:
    pass


```

```python
import subprocess
import time

cmd = 'python test.py'
start = time.time()
ps = []
for i in range(10):
    ps.append(subprocess.Popen(cmd, shell=True))

for proc in ps:
    proc.communicate()
end = time.time()
print(f'time = {end - start}s')  # 1.4s

```

- 然后 在子线程中等待子进程完成,只需要1.4秒,证明是平行运行的

---

- 我们还可以在Python程序里面把数据通过管道发送给子进程所运行的外部命令，
- 然后将那条命令的输出结果获取到Python程序之中
- Popen() ..略

## 第53条　可以用线程执行阻塞式I/O，但不要用它做并行计算

- python的标准实现是CPython 默认带有全局解释器锁,
- 虽然支持多线程,但是解释器字节码本身是单线程的,限制了并行性能
- 所以每次或许只能有一条线程向前推进，而无法实现多头并进
- 不利于通过多线程做并行计算或是给程序提速
- 于GIL机制，虽然每次还是只能有一个线程向前执行，
- 但CPython会确保这些Python线程之间能够公平地轮换执行
- 所以对于阻塞式的io密集型的程序来说还是可以有效果的,因为当io阻塞时,解释器有机会换到其他线程

---

## 第54条　利用Lock防止多个线程争用同一份数据

```python
from threading import Thread


class Count:
    def __init__(self):
        self.count = 0

    def add(self):
        self.count += 1


def worker(how_many, counter: 'Count'):
    for i in range(how_many):
        counter.add()


def main():
    counter = Count()
    ths = [Thread(target=worker, args=(1000000, counter)) for i in range(10)]
    for t in ths:
        t.start()
    for t in ths:
        t.join()
    print(counter.count)  # 8818671


if __name__ == '__main__':
    main()

```

~~~
出现结果不正确的原因在于第一个线程
执行
self.count += 1 的时候,先拿到self.count的值然后加一再赋值给self.count 
结果在赋值之前,另一个线程也更新了self.count,
此时再第一个线程进行更新的时候,就把其他线程的结果覆盖掉了
~~~

```python
from threading import Thread, Lock


class Count:
    def __init__(self):
        self.count = 0
        self.lock = Lock()

    def add(self):
        # 使用threading模块提供的Lock来保证某个操作属于原子操作,不会被别的线程干扰
        with self.lock:
            self.count += 1


def worker(how_many, counter: 'Count'):
    for i in range(how_many):
        counter.add()


def main():
    counter = Count()
    ths = [Thread(target=worker, args=(1000000, counter)) for i in range(10)]
    for t in ths:
        t.start()
    for t in ths:
        t.join()
    print(counter.count)


if __name__ == '__main__':
    main()

```

- 但是加锁会导致性能问题,特别是对于纯cpu密集型任务来说

## 第55条　用Queue来协调各线程之间的工作进度

## 第56条　学会判断什么场合必须做并发

- fan-out与fan-in是最常见的两种并发协调（concurrency coordination）模式，
- 前者用来生成一批新的并发单元，后者用来等待现有的并发单元全部完工

## 第57条　不要在每次fan-out时都新建一批Thread实例

- 每次都手工创建一批线程，是有很多缺点的，
  例如：创建并运行大量线程时的开销比较大，每条线程的内存占用量比较多，
  而且还必须采用Lock等机制来协调这些线程。异常很难调试

## 第58条　学会正确地重构代码，以便用Queue做并发

- 把队列（Queue）与一定数量的工作线程搭配起来，可以高效地实现fan-out（分派）与fan-in（归集）

## 第59条　如果必须用线程做并发，那就考虑通过ThreadPoolExecutor实现

```python
import time
from concurrent.futures import ThreadPoolExecutor, Future
from threading import current_thread


def work_fn(n, nums):
    time.sleep(2)
    print(f'current_thread={current_thread().name},n={n},nums={nums}')
    return f'n={n},nums={nums}'


pools = ThreadPoolExecutor(max_workers=3, thread_name_prefix='pool')
# 使用submit向线程池中提交任务 返回值是一个 concurrent.futures.Future 对象
task1: 'Future' = pools.submit(work_fn, 1, 2)
task2 = pools.submit(work_fn, 2, (2, 3))
print(task1.done())  # 判断一个函数是否已经执行完了
r = task1.result()  # 获得提交的工作函数的返回值 这样子会阻塞当前线程直到获取到结果
print(task1.done())
# task1.cancel() 用于取消任务,如果这个任务已经在执行了,则不可取消
```

```python
import time
from concurrent.futures import ThreadPoolExecutor, Future
from threading import current_thread


def work_fn(n, nums):
    s = f'current_thread={current_thread().name},n={n},nums={nums}'
    if n == 1:
        raise ValueError
    return s


pools = ThreadPoolExecutor(max_workers=3, thread_name_prefix='pool')
# 使用submit向线程池中提交任务 返回值是一个 concurrent.futures.Future 对象
task1: 'Future' = pools.submit(work_fn, 1, 2)

print(task1.done())  # 判断一个函数是否已经执行完了 执行报错不影响它的结果
e = task1.exception()  # 这里可以获取到线程内部的错误
try:
    r = task1.result()  # 使用try语句也可以
except Exception as ee:
    print(ee)
```

- 除了使用result()阻塞的获取结果,更多的使用是
- 使用as_completed一次获取所有一个未来对象的可迭代容器,每次可以抛出一个已完成的对象

```python
import time
from concurrent.futures import ThreadPoolExecutor, Future, as_completed
from threading import current_thread


def work_fn(n, nums):
    time.sleep(10 - n)
    return f'current_thread={current_thread().name},n={n},nums={nums}'


pools = ThreadPoolExecutor(max_workers=10, thread_name_prefix='pool')
all_tasks = [pools.submit(work_fn, i, (i, i + 1)) for i in range(10)]
for f in as_completed(all_tasks):
    print(f.result())
# 按照完成的先后顺序进行迭代
```

- 使用map保证结果顺序

```python
import time
from concurrent.futures import ThreadPoolExecutor, Future, as_completed
from threading import current_thread


def work_fn(n, nums):
    s = f'current_thread={current_thread().name},n={n},nums={nums}'
    # print(s)
    if n == 4:
        time.sleep(10)
    return s


pools = ThreadPoolExecutor(max_workers=10, thread_name_prefix='pool')
results = pools.map(work_fn,
                    [i for i in range(10)],
                    [(i, i + 1) for i in range(10)])
# 得到一个生成器,每个元素和提交的顺序一致
for r in results:
    print(f'---{r}')
# 通过map 无需显式的使用submit,并且结果和迭代的顺序一致
# 其中某一个任务的延时会影响后面的结果的获取,但不影响执行
# 当某一个任务抛出异常的时候,迭代的时候也会抛出这个异常,迭代器也不能继续迭代
```

- 使用线程池的好处,避免频繁手动创建销毁线程的开销
- 结构比较简单

## 第60条　用协程实现高并发的I/O

- python受限于GIl的影响,虽然i/o密集型的任务可以通过线程解决,但是资源占用比较大,
- 高并发场景可以使用协程
- 使用 async 与 await 关键字来实现
- 启动协程是有代价的，就是必须做一次函数调用。
- 协程激活之后，只占用不到1KB内存，所以只要内存足够，协程稍微多一些也没关系 (比线程轻量很多)
- 基本原理与生成器（generator）类似，也就是不立刻给出所有的结果
- 而是等需要用到的时候再一项一项地获取

~~~
协程与线程的区别在于，它不会把这个函数从头到尾执行完，而是每遇到一个await表达式，
就暂停一次，下次继续执行的时候，它会先等待await所针对的那项awaitable操作有了结果
（那项操作是用async函数表示的）
然后再推进到下一个await表达式那里
（这跟生成器函数的运作方式有点像，那种函数也是一遇到yield就暂停）
~~~

```python
# 使用线程池并发的读取文件
from concurrent.futures import ThreadPoolExecutor


def set_up():
    """创建一些模拟文件"""
    for i in range(10):
        with open(f'file{i}.txt', 'w', encoding='utf-8') as f:
            f.write(f'file{i}.txt')


def read_file(file_path):
    with open(file_path, 'r', encoding='utf-8') as f:
        res = f.readlines()
        return res


def main():
    pools = ThreadPoolExecutor(max_workers=10)
    files = [f'file{i}.txt' for i in range(10)]
    res = pools.map(read_file, files)
    for r in res:
        print(r)
    pass


if __name__ == '__main__':
    set_up()
    main()

```

- coroutine function 和 coroutine object,和 coroutine task

```python
import asyncio


# 使用async定义的函数是协程函数
async def fun():
    print('111')
    return


# 直接对协程函数进行调用不会执行函数体中的任何代码而是生成一个协程对象
a = fun()  # type(a) <class 'coroutine'>
print('---')
asyncio.run(a)  # 使用asyncio.run来真正调用函数体中的代码
```

- asyncio.run() 实际上是协程的事件循环的驱动入口

```python
# asyncio.run做了两件事
# 1.建立起even loop事件循环
# 2.把传入的coroutine object建立起成一个task(协程任务)
# 解释器此时会不断寻找所有的task,
# 把可以运行的task向前推进(即t对应的func中的代码)
import asyncio


async def fun():
    print('111')
    print('222')
    return


a = fun()
print('---')
asyncio.run(a)
# 打印结果
# ---
# 111
# 222
```

- 目前只有一个task,协程一般会用在有许多任务需要并发执行的场景,这些任务需要等待
- 当有多个task的时候,当某个task已经需要等待io的时候,可以主动告诉even loop
- 然后even loop将安排其他可以执行的task推进
- task如何告诉even loop 需要等待 await 关键字
- await同时会把后面跟的 coroutine object 注册到 even loop 中

```python
import asyncio
import time


# 1 等待另一个coroutine
async def _sleep():
    await asyncio.sleep(1)  # 这一句不是必须的
    print('222')
    time.sleep(1)
    print('333')


async def fun():
    print('111')
    await _sleep()
    # 从asyncio.run(a)进来后
    # 等待 _sleep() 中的
    print('444')
    await asyncio.sleep(1)  # 2 asyncio.sleep(1)直接使用这个模拟
    print('555')

    return


a = fun()
print('---')
asyncio.run(a)
# 现在程序打印111后将等待三秒再打印222
# ---
# 111 没有等待 fun的第一条
# 222 等待1秒 _sleep()中的asyncio.sleep(1) 此时交出控制权,但是事件循环中没有其他task可以运行
# 333 等待1秒  _sleep()中的time.sleep(1)  这个等待没有交出控制权
# 444 没有等待 333之后_sleep()结束了,fun继续推进直接打印444
# 555 等待1秒 fun中的asyncio.sleep(1)
```

- 所有控制权的转移只能通过await 或者函数结束
- 上面的写法所有任务都在等前一个任务完成,才能继续,并没有并行,
- 因为task是在运行过程中通过await逐个添加到事件循环当中的,
- 当它被添加的同时,也拿到了控制权,所以无法切换
- 要实现并行,使用**asyncio.create_task()**

```python
import asyncio
import datetime


async def say(wath, t):
    await asyncio.sleep(t)
    print(wath)
    return f'{wath, t}'


async def main():
    task1 = asyncio.create_task(say('111', 1))
    task2 = asyncio.create_task(say('222', 2))
    # task1 = say('111', 1)
    # task2 = say('222', 2)
    """注意上面注释的这种,如果使用上面这种写法,
    await 后面跟的是协程对象,
    而使用create_task 则由 create_task将协程对象注册为task,
    当 await 跟的是一个 协程对象 的时候,
    则由await将协程对象注册为协程task,同时这一步将锁定控制权,直到完成才会交出控制权
    当 await 跟的是一个 协程任务 的时候,事件循环只是在等待其有没有完成
    """
    print(datetime.datetime.now())
    r2 = await task2
    print('---')
    r1 = await task1
    # 特意写反的,因为是同时在等待的,
    # 最后task1先完成先打印,总用时是2秒而不是3秒

    print(datetime.datetime.now())
    print(r1, r2)


if __name__ == '__main__':
    asyncio.run(main())

```

- task 类型是Task,是Future的子类
- await 语句可以有接收变量,值就是对应func的返回值
- 当有很多个协程的时候,一个一个的写create_task很啰嗦
- 使用asyncio.gather(),返回值是Future类型
- asyncio.gather()接受多个Task或者coroutine object,
- 或者Future(即可以gather另一个gather产生的对象)
- 当await Future 的时候,返回一个list,结果是这里面所有的task的返回值(会等待所有的任务完成)

```python
import asyncio
import datetime


async def say(wath, t):
    await asyncio.sleep(t)
    print(wath)
    return f'{wath, t}'


async def main():
    t1 = asyncio.gather(say('111', 1))
    t2 = say('222', 2)
    t3 = asyncio.create_task(say('333', 3))

    r = asyncio.gather(t1, t2, t3)

    print(datetime.datetime.now())
    res = await r
    print(datetime.datetime.now())
    print(res)


if __name__ == '__main__':
    asyncio.run(main())

```

## 第61条　学会用asyncio改写那些通过线程实现的I/O

## 第62条　结合线程与协程，将代码顺利迁移到asyncio

## 第63条　让asyncio的事件循环保持畅通，以便进一步提升程序的响应能力

## 第64条　考虑用concurrent.futures实现真正的并行计算

- Python内置的multiprocessing模块提供了多进程机制
- 这种机制很容易通过内置的concurrent.futures模块来使用

```python
import os
from datetime import datetime
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

NUMS = [(1797687, 1536930), (1074072, 2956786), (1553718, 2605127),
        (2033811, 1749094), (1005581, 2468564), (2473882, 2309331),
        (2363957, 1930368), (1520438, 1972264), (1907306, 1348950),
        (1714906, 2712775),
        (1797687, 1536930), (1074072, 2956786), (1553718, 2605127),
        (2033811, 1749094), (1005581, 2468564), (2473882, 2309331),
        (2363957, 1930368), (1520438, 1972264), (1907306, 1348950),
        (1714906, 2712775), (1797687, 1536930), (1074072, 2956786),
        (1553718, 2605127),
        (2033811, 1749094), (1005581, 2468564), (2473882, 2309331),
        (2363957, 1930368), (1520438, 1972264), (1907306, 1348950),
        (1714906, 2712775),
        (1797687, 1536930), (1074072, 2956786), (1553718, 2605127),
        (2033811, 1749094), (1005581, 2468564), (2473882, 2309331),
        (2363957, 1930368), (1520438, 1972264), (1907306, 1348950),
        (1714906, 2712775), (1797687, 1536930), (1074072, 2956786),
        (1553718, 2605127),
        (2033811, 1749094), (1005581, 2468564), (2473882, 2309331),
        (2363957, 1930368), (1520438, 1972264), (1907306, 1348950),
        (1714906, 2712775)
        ]


# 求解两个数的最大公约数
def gcd(two_num):
    a: int
    b: int
    a, b = two_num
    low = min(a, b) - 1
    while low > 1:
        if a % low == 0 and b % low == 0:
            return low
        low -= 1
    else:
        return None


if __name__ == '__main__':
    start = datetime.now()
    res = list(map(gcd, NUMS))
    end = datetime.now()
    print(end - start)  # 0:00:06.338749
    print(res)

    th_pool = ThreadPoolExecutor(os.cpu_count())
    start = datetime.now()
    res = list(th_pool.map(gcd, NUMS))
    end = datetime.now()
    print(end - start)  # 0:00:06.337512
    print(res)

    ph_pool = ProcessPoolExecutor(os.cpu_count())
    start = datetime.now()
    res = list(ph_pool.map(gcd, NUMS))
    end = datetime.now()
    print(end - start)  # 0:00:01.375344
    print(res)

```

- 对于纯cpu计算的任务来说,多进程才能发挥多核优势
- 然而这样做的开销很大，因为它必须在上级进程与子进程之间做全套的序列化与反序列化处理
- 可以使用高级特性避免数据拷贝和(反)序列化

# 第8章　稳定与性能

## 第65条　合理利用try/except/else/finally结构中的每个代码块

~~~
try/finally形式的复合语句可以确保，无论try块是否抛出异常，finally块都会得到运行。

如果某段代码应该在前一段代码顺利执行之后加以运行，那么可以把它放到else块里面，
而不要把这两段代码全都写在try块之中。
这样可以让try块更加专注，同时也能够跟except块形成明确对照：

except块写的是try块没有顺利执行时所要运行的代码。

如果你要在某段代码顺利执行之后多做一些处理，
然后再清理资源，那么通常可以考虑把这三段代码分别放在try、else与finally块里
~~~

## 第66条　考虑用contextlib和with语句来改写可复用的try/finally代码

- 让一个对象支持with语句,标准做法是实现__enter__和__exit__方法
- enter 的返回值 可以被 with ... as xxx 接收,(一般就返回这个类的实例就行)
- 通过with块临时改变logger的级别 需要设计一个上下文管理类,在退出的时候恢复原状

```python
import logging
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from logging import Logger as _TypeLogger

logging.basicConfig(format='[%(asctime)s] [%(levelname)s] [%(name)s] [%(message)s]')
logger = logging.getLogger("logger")
logger.setLevel(logging.WARNING)


def foo(m):
    logger.info(m)
    logger.debug(m)
    logger.warning(m)


class DebugLog:
    def __init__(self, __logger: '_TypeLogger'):
        self._logger = __logger
        self._old = __logger.getEffectiveLevel()

    def __enter__(self):
        self._logger.setLevel(logging.DEBUG)

    def __exit__(self, exc_type, exc_val, exc_tb):
        self._logger.setLevel(self._old)


if __name__ == '__main__':
    foo('1')
    with DebugLog(logger):
        foo('2')
    foo('3')
```

- 使用 from contextlib import contextmanager 来定义一个函数实现上下文管理器,比写一个类简单
- 使用的时候没区别
- yield 可以跟 一个对象obj 在with语句中可以使用 with xxxx() as obj:

```python
from contextlib import contextmanager


@contextmanager
def debug_logging(__logger: '_TypeLogger'):
    _old = __logger.getEffectiveLevel()
    __logger.setLevel(logging.DEBUG)
    try:
        yield
    finally:
        __logger.setLevel(_old)

# with debug_logging(logger):
#        foo('4')
#    foo('5')
```

- __exit__方法中的这个三个参数‘exc_type, exc_val, exc_tb’
- 代表 异常类型，异常值和异常的trackback
- 如果with语发生异常，但不希望抛出，__exit__方法应该返回return True，
- 相当于with语句自动做一个try，except处理，

```python
import traceback


def foo(n):
    if n == 0:
        raise ValueError('n不能等于0')
    print(f'{n}' * 3)


class WithNoRaise:
    def __enter__(self):
        ...

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type:
            print(traceback.format_exc())
        return True


class WithRaise:
    def __enter__(self):
        ...

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type:
            print(f'exc_tb={traceback.format_exc()}')
        return False


if __name__ == '__main__':
    with WithNoRaise():
        foo(0)
    foo(1)

    # with WithRaise():
    #     foo(0)
    # foo(1)

    # 由于没有处理异常,这个地方的foo(1)不会被执行
```

## 第67条　用datetime模块处理本地时间，不要用time模块

- datetime模块处理时间更好用,且内置许多常用方法

```python
import datetime
import time

# 获取当前日期和时间
print(datetime.datetime.now())

print(datetime.date.today())
# 创建一个日期对象
d = datetime.date(2019, 4, 13)
print(d)
# 创建一个日期+时间对象
d = datetime.datetime(2019, 4, 13, 5, 35, 22, 999)
print(d)
# 从时间戳转换
time_stamp = time.time()  # float
print('utc', datetime.datetime.utcfromtimestamp(time_stamp))  # 拿到的是utc时间
print('本地', datetime.datetime.fromtimestamp(time_stamp))

# timedelta对象表示两个日期或时间之间的时差。
t1 = datetime.datetime.now()
time.sleep(1.5)
t2 = datetime.datetime.now()
delta = t2 - t1
print(delta, repr(delta))
'0:00:01.501804   datetime.timedelta(seconds=1, microseconds=501804)'

# 直接构造一个时间差
delta = datetime.timedelta(days=0.5, hours=0.5)
print(delta, repr(delta))
'12:30:00 datetime.timedelta(seconds=45000)'

# 获得时间差的总秒数
print(delta.total_seconds())

# 时间对象转字符串
t = datetime.datetime.now()
print(t)
print(str(t))
print(t.strftime("%Y-%m-%d %H:%M:%S.%f"))  # 默认的格式

# 从字符串转时间
d = datetime.datetime.strptime('2024-10-11 10:57:22.433206', "%Y-%m-%d %H:%M:%S.%f")
print(d)

```

## 第68条　用copyreg实现可靠的pickle操作

~~~
Python内置的pickle模块，只适合用来在彼此信任的程序之间传递数据，
以实现对象的序列化与反序列化功能。如果对象所在的这个类发生了变化（例如增加或删除了某些属性）
那么程序在还原旧版数据的时候，可能会出现错误。
把内置的copyreg模块与pickle模块搭配起来使用，可以让新版的程序兼容旧版的序列化数据。
~~~

## 第69条　在需要准确计算的场合，用decimal表示相应的数值

- python 默认的浮点精度为双精度浮点数类型遵循IEEE 754规范
- 在精度要求较高且需要控制舍入方式的场合（例如在计算费用的时候）可以考虑使用Decimal类
- 使用str构造确保获得高精度值

```python
import decimal

a = decimal.Decimal('1.1')
b = decimal.Decimal(1.1)
print(a, b)
'1.1 1.100000000000000088817841970012523233890533447265625'
```

## 第70条　先分析性能，然后再优化

- Python内置了两种profiler，一种是由profile模块提供的纯Python版本，
- 还有一种是由cProfile模块提供的C扩展版本

```python
# 一个低效的插入排序
import random


def insert_value(value: int, result: list):
    if not result:
        result.append(value)
        return
    for i, n in enumerate(result):
        if value < n:
            result.insert(i, value)
            return
    result.append(value)


def insert_sort(a: list):
    result = []
    for value in a:
        insert_value(value, result)
    return result


def main():
    a_list = [random.randint(0, 999999) for i in range(int(1e4))]
    r = insert_sort(a_list)


if __name__ == '__main__':
    from cProfile import Profile

    profiler = Profile()
    profiler.runcall(main)
    from pstats import Stats

    stats = Stats(profiler)
    stats.strip_dirs()
    stats.sort_stats('cumulative')
    stats.print_stats()
    """
             70463 function calls in 1.004 seconds
    
       Ordered by: cumulative time
    
       ncalls  tottime  percall  cumtime  percall filename:lineno(function)
            1    0.000    0.000    1.004    1.004 eee.py:23(main)
            1    0.002    0.002    0.994    0.994 eee.py:16(insert_sort)
        10000    0.984    0.000    0.992    0.000 eee.py:5(insert_value)
            1    0.002    0.002    0.010    0.010 eee.py:24(<listcomp>)
        10000    0.002    0.000    0.009    0.000 random.py:244(randint)
         9987    0.008    0.000    0.008    0.000 {method 'insert' of 'list' objects}
        10000    0.004    0.000    0.007    0.000 random.py:200(randrange)
        10000    0.002    0.000    0.003    0.000 random.py:250(_randbelow_with_getrandbits)
        10459    0.001    0.000    0.001    0.000 {method 'getrandbits' of '_random.Random' objects}
        10000    0.000    0.000    0.000    0.000 {method 'bit_length' of 'int' objects}
           13    0.000    0.000    0.000    0.000 {method 'append' of 'list' objects}
            1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
    """
    """
    关注累积时间(cumtime)
    累积时间列显示了每个函数及其调用的子函数执行的总时间
    insert_sort 函数被调一次,花费0.994s
    insert_value 函数被调10000次,花费0.992s 而insert_value只在insert_sort中调用
    表明 insert_sort 慢的原因是 太多次调用 insert_value
    
    """
    stats.print_callers()
    """
    
   Ordered by: cumulative time

Function                                            was called by...
                                                        ncalls  tottime  cumtime
eee.py:23(main)                                     <- 
eee.py:16(insert_sort)                              <-       1    0.002    1.011  eee.py:23(main)
eee.py:5(insert_value)                              <-   10000    1.002    1.009  eee.py:16(insert_sort)
eee.py:24(<listcomp>)                               <-       1    0.002    0.010  eee.py:23(main)
random.py:244(randint)                              <-   10000    0.002    0.009  eee.py:24(<listcomp>)
{method 'insert' of 'list' objects}                 <-    9989    0.008    0.008  eee.py:5(insert_value)
random.py:200(randrange)                            <-   10000    0.004    0.007  random.py:244(randint)
random.py:250(_randbelow_with_getrandbits)          <-   10000    0.002    0.003  random.py:200(randrange)
{method 'getrandbits' of '_random.Random' objects}  <-   10446    0.001    0.001  random.py:250(_randbelow_with_getrandbits)
{method 'bit_length' of 'int' objects}              <-   10000    0.000    0.000  random.py:250(_randbelow_with_getrandbits)
{method 'append' of 'list' objects}                 <-      11    0.000    0.000  eee.py:5(insert_value)
{method 'disable' of '_lsprof.Profiler' objects}    <- 

    """
    """
    受调用的函数写在左边，把调用这个函数的其他函数写在右边,
    这个可以分析同一个函数被其他函数的调用情况
    """

```

## 第71条　优先考虑用deque实现生产者-消费者队列

## 第72条　考虑用bisect搜索已排序的序列

## 第73条　学会使用heapq制作优先级队列

```python
from heapq import heappush, heappop

if __name__ == '__main__':
    heap = []
    heappush(heap, 2)
    heappush(heap, 3)
    heappush(heap, 1)

    while heap:
        print(heappop(heap))  # 1 2 3

```

- 下面是错误的

```python
from heapq import heappush, heappop

if __name__ == '__main__':
    heap = [3, 2, 1]

    while heap:
        print(heappop(heap))
```

- 如果本身已经有元素了,需要先构造成优先级队列

```python
from heapq import heappush, heappop, heapify

if __name__ == '__main__':
    heap = [3, 2, 1]
    heapify(heap)
    while heap:
        print(heappop(heap))
```

- 这个效果和sort是一样的,但是比sort快 O(n)时间 sort需要O(nlog(n))时间
- queue 模块提供了一个线程安全的优先队列类型

```python
from queue import PriorityQueue

if __name__ == '__main__':
    q = PriorityQueue()
    q.put(3)
    q.put(2)
    q.put(1)
    while not q.empty():
        print(q.get())
```

## 第74条　考虑用memoryview与bytearray来实现无须拷贝的bytes操作

# 第9章　测试与调试

## 第75条　通过repr字符串输出调试信息

- 当需要把一个python对象进行答应表示的时候比如print(x),str(x),f'{x}' 会调用__str__
- __ str__ 默认调用__repr__,除非自定义重写
- 合格的__repr__ 应当可以使用eval()进行还原

```python
class A:
    def __init__(self, x):
        self.x = x

    def __repr__(self):
        return f"{self.__class__.__name__}({self.x})"


a = A(1)
print(a)  # A(1)
b = eval(repr(a))
print(b)  # A(1)

```

## 第76条　在TestCase子类里验证相关的行为

## 第77条　把测试前、后的准备与清理逻辑写在setUp、tearDown、setUp-Module与tearDownModule中，以防用例之间互相干扰

## 第78条　用Mock来模拟受测代码所依赖的复杂函数

## 第79条　把受测代码所依赖的系统封装起来，以便于模拟和测试

## 第80条　考虑用pdb做交互调试

- 触发调试器的指令就是Python内置的breakpoint函数

## 第81条　用tracemalloc来掌握内存的使用与泄漏情况

# 第10章　协作开发
## 第82条　学会寻找由其他Python开发者所构建的模块

## 第83条　用虚拟环境隔离项目，并重建依赖关系
## 第84条　每一个函数、类与模块都要写docstring
- 用类型注解来简化docstring
```
# 可以把类型信息写在声明位置(类型注解)
    def get_clarity(cls, im_gray):
        """从一张灰度图计算清晰度

        :param im_gray: 一张灰度图
        :type im_gray: np.ndarray
        :return: 0-1 最不清晰-最清晰
        :rtype: float
        """
        ...
```
## 第85条　用包来安排模块，以提供稳固的API
## 第86条　考虑用模块级别的代码配置不同的部署环境
~~~
程序通常需要部署到许多种环境里面，
无论在哪一种环境之中运行程序，都必须先准备好相关的资源，并做出适当的配置。

可以像编写普通的Python语句那样，直接在模块作用域书写配置逻辑，以定制该模块的内容，
从而针对不同的环境做出适当的部署。

还可以根据其他一些外部因素来调整模块的内容，

例如通过sys或os模块查询与操作系统有关的信息，并据此定制该模块
或者config.yaml之类的配置文档,并在项目内部编写解析配置文件,并根据配置文件选择不同的逻辑
~~~

## 第87条　为自编的模块定义根异常，让调用者能够专门处理与此API有关的异常


## 第88条　用适当的方式打破循环依赖关系
- 重构代码避免循环依赖
- 调整import语句的位置
- 把模块划分成引入-配置-运行这样三个环节
- 动态引入(可能性能问题)

## 第89条　重构时考虑通过warnings提醒开发者API已经发生变化

## 第90条　考虑通过typing做静态分析，以消除bug
- mypy
