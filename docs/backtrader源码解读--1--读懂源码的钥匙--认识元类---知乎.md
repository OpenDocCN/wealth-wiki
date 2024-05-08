<!--yml
category: 交易
date: 2023-09-17 20:00:27
-->

# backtrader源码解读 (1)：读懂源码的钥匙——认识元类 - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/594948193](https://zhuanlan.zhihu.com/p/594948193)

backtrader是著名的基于Python语言的量化工具，它可以帮助我们实现投资策略的回测、参数优化以及可视化，甚至可以接入实盘交易。

现如今有许多backtrader的使用教程，而解读backtrader源码的文章却寥寥无几。事实上，阅读backtrader的底层代码可以让我们理解它的工作原理，从而在使用它进行量化投资时实现更个性化的配置、更高效的debug、以及在必要时提高运行性能。

本系列文章是作者的呕心之作，将尽可能细致地对backtrader的源码进行解析。此外，作者也会不断分享量化投资的心得，欢迎大家进行关注与分享。

* * *

## 0 引言

在开始之前，我们来速览一下backtrader的源码。在安装并导入backtrader后，通过__path__属性便可以拿到源码的路径。

```
import backtrader as bt
print(bt.__path__)
```

> ['D:\\Application\\Anaconda\\lib\\site-packages\\backtrader']

按照路径打开文件夹，backtrader所有的秘密都藏在下图中的py文件和文件夹里。尽管它们现在看起来杂乱无章、无从下手，但只要我们掌握其内在的逻辑，理解起来会越来越容易。

![](img/46fb56abd51636e8b02fdce93c0e1b79.png)

作为第一步，我们需要理解**元类**(metaclass)。

事实上，backtrader采用了元编程的技术，在代码中引入了大量的元类。元类的使用节省了大量重复的代码，并实现了一些相对复杂的功能，然而代价就是让代码晦涩难懂。可以说，理解元类是畅读backtrader源码的基础，也是第一个难点。

在介绍元类之前，我们简单复习有关类的知识。在包括Python的多数语言中，类 (class) 用来创建对象 (object)。在example 1中，我们定义了类Student，并且让类Student创建对象s。

请注意，为了区分单一案例中的多次打印，我们会在每次打印的内容前加上数字标签。

```
# example 1
class Student:
    pass

s = Student()

print('[1]', s)
```

> [1] <__main__.Student object at 0x0000020BBA1DC4F0>

这里，我们称Student为类，是因为它可以创建对象。

在Python中，**万物皆对象**。所以，**类也是对象**。

那么，既然类Student本身也是对象，那么它一定也是由某个"类"所创建。不卖关子，这个"类"是type，而type是一个元类。

## 1 重新认识type

我们对type比较熟悉的功能是：把某个对象作为参数传给type，返回该对象的类型，见example 2。

```
# example 2
a = 1
print('[1]', type(a))

b = 'apple'
print('[2]', type(b))

class Student:
    pass
s = Student()
print('[3]', type(s))
```

> [1] <class 'int'>
> [2] <class 'str'>
> [3] <class '__main__.Student'>

然而，type还有一个完全不同的功能，那就是**动态创建类**，具体的方式如下：

type接受三个参数，其中，

*   name：字符串，类的名称；
*   bases：元组，其元素为类的父类；
*   dct：字典，键为类的属性名或方法名，值为对应的属性值或函数；

并返回一个类。事实上，在我们使用class关键字定义类的时候，背后调用的就是type。

这么说有一些空洞，我们来举一个具体的例子。

在example 3.1中，我们用常规的方法，也就是class关键字，来定义类Dog1：类Dog1继承类Animal，并拥有类变量isAnimal，初始化方法__init__，以及实例方法run。

```
# example 3.1
class Animal:
    pass

class Dog1(Animal):           
    isAnimal = True           

    def __init__(self, name):
        self.name = name

    def run(self):
        print(f'Dog {self.name} is running')

print('[1]', Dog1.__mro__)

d1 = Dog1('Puppy')
print('[2]', d1.isAnimal)
d1.run()
```

> [1] (<class '__main__.Dog1'>, <class '__main__.Animal'>, <class 'object'>)
> [2] True
> Dog Puppy is running

在example 3.2中，我们用type来创建类Dog2。

请注意，我们给type传递了三个参数：第一个参数'Dog2'代表创建类的名称；第二个参数bases是元组，其元素为创建类的父类；第三个参数dct是字典，该字典的键和值对应的是创建类的变量名和值，或者方法名和函数。

我们把type的返回值赋予Dog2。可以看到，Dog2的继承关系以及可实现的功能和Dog1一致。

```
# example 3.2
class Animal:
    pass

bases = (Animal, )

def __init__(self, name):
    self.name = name

def run(self):
    print(f'Dog {self.name} is running')

dct = {'isAnimal': True, 
         '__init__': __init__, 
         'run': run}

Dog2 = type('Dog2', bases, dct)

print('[1]', Dog2.__mro__)

d2 = Dog2('Kala')
print('[2]', d2.isAnimal)
d2.run()
```

> [1] (<class '__main__.Dog2'>, <class '__main__.Animal'>, <class 'object'>)
> [2] True
> Dog Kala is running

## 2 什么是元类

将example 3.2中动态创建类和类创建对象的两行代码放在一起进行比较，我们可以发现“上一级”创建“下一级”的方式非常类似：type创建类就好比类创建对象。

*   type创建类Dog2：type后面加括号，括号里传入参数

```
Dog2 = type('Dog2', bases, attrs)
```

*   类Dog2创建对象d2：Dog2后面加括号，括号里传入参数

请记住，在Python中，**万物皆对象**。只要是对象，那么它一定是由某个类所创建。我们可以通过对象的__class__属性来查看它是由什么类所创建，见example 4.1。

```
# example 4.1
a = 1
print('[1]', a.__class__)

b = 'apple'
print('[2]', b.__class__)

def func(p1, p2):
    return p1 + p2
print('[3]', func.__class__)

class Student:
    pass
s = Student()
print('[4]', s.__class__)
```

> [1] <class 'int'>
> [2] <class 'str'>
> [3] <class 'function'>
> [4] <class '__main__.Student'>

我们再次重申：在Python中，**万物皆对象**。

这里的万物，是真正字面上的everything，它不仅包括例如example 4.1中的整数a、字符串b、函数func、或者是自定义类的实例对象s，也包括类本身。也就是说，类也是对象，它也是由某个类所创建。

**所谓元类，是创建类的类。**

在Python中，type是内置用来创建类的元类。如果不加以声明，所有的类默认都是由type来创建。

在example 4.2中，我们可以看到，创建int类、str类、function类、Student类的类都是type。

```
# example 4.2
print('[1]', a.__class__.__class__)
print('[2]', b.__class__.__class__)
print('[3]', func.__class__.__class__)
print('[4]', s.__class__.__class__)
```

> [1] <class 'type'>
> [2] <class 'type'>
> [3] <class 'type'>
> [4] <class 'type'>

## 3 自定义元类

在了解了什么是元类以及type是默认创建类的元类之后，我们提出这样一个问题：我们能否不用type来创建类，而是自定义的元类来创建类？

答案是yes！

在Python 3中，自定义元类创建类的基本方法为:

*   第一步：创建元类，一个类必须继承type才能使其成为一个元类；
*   第二步：创建类，使用关键词参数metaclass指定创建它的元类。

在example 5中，我们定义了元类MyMetaClass (因为它继承了type) ，并且指定它为创建类MyClass的元类。

```
# example 5
class MyMetaClass(type):
    pass

class MyClass(metaclass = MyMetaClass):
    pass
```

这里我们查看类MyClass的__class__属性，显示的是元类MyMetaClass。

```
# example 5 - continued
print('[1]', MyClass.__class__)
```

> [1] <class '__main__.MyMetaClass'>

我们再深挖一步，如果将元类MyMetaClass视作一个对象 (请始终牢牢记住：在Python中，万物皆对象) ，创建它的类是什么？通过查看元类MyMetaClass的__class__属性，我们得到：type创建了元类MyMetaClass。

```
# example 5 - continued
print('[1]', MyMetaClass.__class__)
```

> [1] <class 'type'>

所以说，元类也是由type所创建。从这个层面上说，元类也是类。事实上，example 5第2至3行等价于下面的语句，这么一看就比较容易理解了。

```
MyMetaClass = type('MyMetaClass', (type, ), {})
```

## 4 元类的__new__方法

在学习了如何自定义元类创建类之后，我们不禁要问：这么做的意义是什么？

事实上，使用元类最主要的目的是**在元类创建类的过程中按照我们的预设自动修改类**。在实际应用场景中，大量不面向用户的“脏活累活”都被放在元类中处理，从而可以最终展现给用户一个简洁且直观的界面。这在backtrader中的运用非常常见，我们在后续的讲解中就会再次提及。

具体来说，实现上述目的主要依赖元类的__new__方法，该方法负责创建类：它将创建类所需要的“原料”作为参数传入，并返回一个类。

另外，在类的创建过程中，元类的__new__方法和__init__方法会被先后调用，前者负责创建类，后者负责类的初始化。在实际使用中，大多类的初始化工作都可以放在__new__方法中进行，所以为了代码简洁往往只需要定义__new__方法。在backtrader中，元类__new__方法的使用频率要远远高于元类__init__方法。

接下来，我们通过一个例子来详细了解元类的__new__方法。在example 6中，我们定义了元类MyMetaClass，并重写了它的__new__方法。

这里需要注意两点：

1.  我们让__new__方法接受动态参数*args，即无论__new__方法接受多少个位置参数都会打包进一个元组给args，我们再通过for循环依次打印args内的元素以展示元类的__new__方法创建类所需要的“原料”；
2.  __new__方法需要返回一个对象，这一步还是交给"专业人士"type来做，我们会将所需要的参数args打散传递给type.__new__。

```
# example 6
class MyMetaClass(type):
    def __new__(*args):
        for i, item in enumerate(args): 
            print(f'[{i}] {item}') 
        return type.__new__(*args)
```

定义好元类MyMetaClass之后，我们让该元类创建类MyClass：该类的父类是类MyBaseClass，并拥有类变量var和实例方法func。

```
# example 6 - continued
class MyBaseClass:
    pass

class MyClass(MyBaseClass, metaclass = MyMetaClass):
    var = 1
    def func(self):
        pass
```

> [0] <class '__main__.MyMetaClass'>
> [1] MyClass
> [2] (<class '__main__.MyBaseClass'>,)
> [3] {'__module__': '__main__', '__qualname__': 'MyClass', 'var': 1, 'func': <function MyClass.func at 0x00000221682CC040>}

通过example 6的打印结果，我们可以知晓，类MyClass的创建过程中调用了元类MyMetaClass的__new__方法，该方法接受四个参数，分别为：

1.  元类MyMetaClass自身；
2.  创建类的名称；
3.  创建类的父类组成的元组；
4.  创建类的属性名或方法名为键，对应的属性值或函数为值所组成的字典。

由于__new__方法是静态方法，所以无论是在定义还是调用时，第一个参数永远是定义它的类本身。另外，__new__方法第二、三、四个参数就是type创建类所接受的参数。在下文中，我们让元类的__new__方法通过四个形参：meta、name、bases、dct来接受实参，这里形参的命名与backtrader中一致。

在example 6中，我们重写了元类的__new__方法，让它做了除了创建类之外的事情：打印接受的参数。事实上，元类可以做更复杂的事情，最常见的方式是在__new__方法中改变接受的参数。在example 7中，我们用元类实现了这样的功能：元类创建类的属性名和方法名，只要不是以双下划线开头，均自动转化为大写。

```
# example 7
class UpperMetaClass(type):
    def __new__(meta, name, bases, dct):
        print('[1]', dct)

        upper_dct = {
            k if k.startswith("__") else k.upper(): v
            for k, v in dct.items()
        }
        return type.__new__(meta, name, bases, upper_dct)

class MyClass(metaclass = UpperMetaClass):
    var = 1
    def func(self):
        pass

print('[2]', MyClass.__dict__)
```

> [1] {'__module__': '__main__', '__qualname__': 'MyClass', 'var': 1, 'func': <function MyClass.func at 0x00000221683B5550>}
> [2] {'__module__': '__main__', 'VAR': 1, 'FUNC': <function MyClass.func at 0x00000221683B5550>, '__dict__': <attribute '__dict__' of 'MyClass' objects>, '__weakref__': <attribute '__weakref__' of 'MyClass' objects>, '__doc__': None}

具体来说，example 7中功能的实现经历了以下几个步骤：

1.  我们在类MyClass中定义了属性var和方法func，随后元类UpperMetaClass介入类MyClass的创建；
2.  这些属性名或方法名与对应的值或函数组成的键值对构成的字典传给了元类UpperMetaClass的__new__方法的形参dct；
3.  在元类UpperMetaClass的__new__方法内部，我们对字典dct进行了修改：只要dct的键不是以双下划线开头则转化为大写，并将修改后的字典赋给upper_dct；
4.  在将参数传递给type.__new__创建类时，我们将dct替换成upper_dct，随后类MyClass创建完成。

通过上面的步骤，我们可以理解元类是如何实现"黑魔法"的：

1.  介入类的创建；
2.  对类进行修改；
3.  返回修改后的类。

以上的工作机制在backtrader中运用得非常广泛。

## 5 小结

元编程是backtrader的底层技术，理解元类是畅读backtrader源码的基础。本文作为backtrader源码解读系列文章的第一篇，根据元类在backtrader中的应用对重要知识点进行了介绍，这其中包括type动态创建类、自定义元类、元类常用工作机制之__new__方法等。

在下一篇文章中，我们将继续这一主题，介绍元类常用工作机制之__call__方法以及元类和继承的关系。欢迎大家的关注和留言！