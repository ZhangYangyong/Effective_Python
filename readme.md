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


def _main():
    # 从一个目录中读取文件并统计行数
    # 创建一些模拟文件
    temp_dir = Path('./temp_dir')
    temp_dir.mkdir(exist_ok=True)
    for i in range(10):
        with open(f'./temp_dir/file_{i}.txt', 'w') as f:
            f.write('\n' * random.randint(0, 100))  # 写入行换行符

    # 首先编写一个辅助函数从目录中构建多个PathInputData实例
    inputs = build_input_data(temp_dir)
    # 再编写一个函数从每个InputData创建LineCountWorker
    works = creat_works(inputs)
    # 最后编写一个函数来分发任务,在多线程中执行map_reduce
    print(execute(works))

```
