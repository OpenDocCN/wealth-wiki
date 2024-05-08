<!--yml
category: 交易
date: 2023-09-17 20:00:18
-->

# backtrader源码解读 (2)：读懂源码的钥匙——元类进阶 - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/597309489](https://zhuanlan.zhihu.com/p/597309489)

backtrader采用了元编程的技术，在代码中引入了大量的元类。在上一篇文章中，我们了解到元类就是创建类的类，以及元类的__new__方法是如何介入类的创建并对所创建的类进行修改的。本文将补齐畅读backtrader源码所需要的其它有关元类的知识点。

## 1\. 元类的__call__方法

__call__方法是Python中特殊的实例方法，它用于**对实例对象的( )运算符进行设定**："实例对象( )"相当于实例对象调用__call__方法，即"实例对象.__call__( )"。

请注意，和其他实例方法一样，在类中定义__call__方法时，必须要有一个形参并将其放在首位，一般命名为self；实例对象调用__call__方法时，该实例对象与self自动绑定，无须再给self赋值。

在example 1中，我们在类MyClass中定义了__call__方法，该方法接受参数num并连同调用者一并打印，类MyClass的实例对象myobj便成为了可调用对象，即可以像调用函数一样将( )运用到myobj之后，且执行效果等同于myobj调用__call__方法。

```
# example 1
class MyClass:
    def __call__(self, num):
        print(f'[{num}] {self}')

myobj = MyClass()

myobj(1)
myobj.__call__(2)
```

> [1] <__main__.MyClass object at 0x0000017DCE6B7790>
> [2] <__main__.MyClass object at 0x0000017DCE6B7790>

在元类中定义__call__方法实现同样的功能——设定实例对象的( )运算符。有趣的是，元类所创建的实例对象是类，而"类( )"，我们再熟悉不过，会创建实例对象。所以说，**元类的__call__方法可以用来控制该元类所创建的类去创建实例对象的过程**。

在example 2.1中，我们首先在元类MyMetaClass中定义__call__方法，该方法实现对调用者的打印。请注意，这里__call__方法的第一个形参没有使用惯用的self，而是使用了cls。这是由于该__call__方法的调用者是类，且这里形参的命名与backtrader中一致。

接下来，我们定义类MyClass，并指定由元类MyMetaClass创建。也就是说，类MyClass是元类MyMetaClass的实例对象。

最后，我们像往常那样，试着让类MyClass创建实例对象myobj。在这个过程中，"MyClass( )"就相当于"MyClass.__call__( )"，就会按照定义把调用者类MyClass打印出来。值得重点注意的是，由于元类MyMetaClass的__call__方法没有返回值，这意味着"MyClass( )"没有返回值，所以myobj是一个空值。

```
# example 2.1
class MyMetaClass(type):
    def __call__(cls):
        print('[1]', cls)

class MyClass(metaclass = MyMetaClass):
    pass

myobj = MyClass()
print('[2]', myobj is None)
```

> [1] <class '__main__.MyClass'>
> [2] True

如果想要语句"myobj = MyClass( )"实现往常的创建实例对象的功能，我们需要让元类的__call__方法返回一个实例对象。在example 2.2中，元类MyMetaClass的__call__方法相比较example 2.1新增几行代码，我们重新梳理整个流程：

1.  "MyClass( )"就相当于类MyClass去调用其元类MyMetaClass的__call__方法，且类MyClass与形参cls自动绑定；
2.  接下来，类MyClass调用__new__方法创建实例对象，并将返回结果赋给obj；
3.  在obj最终被返回之前，为它动态添加实例属性attr。

从最后的结果来看，实例对象myobj刚被创建就“自动”拥有了实例属性attr。

```
# example 2.2
class MyMetaClass(type):
    def __call__(cls):
        print('[1]', cls)
        obj = cls.__new__(cls)
        obj.attr = 1
        return obj

class MyClass(metaclass = MyMetaClass):
    pass

myobj = MyClass()
print('[2]', myobj)
print('[3]', myobj.attr)
```

> [1] <class '__main__.MyClass'>
> [2] <__main__.MyClass object at 0x000001842EF3E100>
> [3] 1

总的来说，**元类的__new__方法介入类的创建，元类的__call__方法介入 (元类所创建的) 类创建实例对象的过程**。在backtrader中，元类的__new__方法和__call__方法相互配合，使用起来相当灵活。用户可以使用较为直白的语句进行策略设计和策略回测，这归功于许多类似于数据清洗、格式规整等极其繁琐的工作都放在了元类的这两个方法中"暗中"进行，从而优化了用户的代码编写环境。

## 2\. 元类和继承

在Python面向对象编程中，继承解决的是类和类之间代码冗余的问题，并可以让代码更加具有层次感和逻辑性。由于backtrader是一个相对复杂的框架，继承的使用非常之多，在这之中不光有类与类的继承，还有元类之间的继承，再加上我们先前介绍的元类创建类，这些类似"套娃"一样的关系可能容易把人绕晕。不要着急，我们慢慢来梳理。

首先，我们需要声明，元类的继承和类的继承采用同样的规则，毕竟从元类是由type所创建的角度来说，元类也是类。example 3是一个元类继承元类并由继承元类创建类的案例，有以下几个点需要留意：

*   **继承的基本运用**：元类MetaClass2继承元类MetaClass1，MetaClass1中定义了__call__方法而MetaClass2没有，由于继承关系，在需要调用MetaClass2的__call__方法时就可以调用MetaClass1的；同理，元类MetaClass3继承元类MetaClass2，MetaClass3中也没有定义__call__方法，由于继承关系可传递，需要调用MetaClass3的__call__方法时也可以调用MetaClass1的；
*   **方法的重写**：元类MetaClass2和元类MetaClass3都定义了__new__方法，这涉及到方法的重写，即子类中重新定义父类中的方法，调用MetaClass3的__new__方法时会完全按照MetaClass3中的定义来执行；
*   **super的使用**：super是Python的内置类，子类中可以使用super调用父类的方法。"完整版"的super接受两个参数：第二个参数决定调用方法的对象并确定使用哪一条MRO链，第一个参数决定在MRO链中从哪个位置寻找其最近的父类以调用该父类的方法。backtrader中使用的是"完整版"super。另外，super在类的内部使用时还有"简洁版"，可以不接受参数：这种情况下，第一个参数默认为super所在的类，第二个参数默认为super所在的方法的第一个参数。

```
# example 3
class MetaClass1(type):
    def __call__(cls):
        print('[1] MetaClass1.__call__')
        obj = cls.__new__(cls)
        obj.attr = 1
        return obj

class MetaClass2(MetaClass1):
    def __new__(meta, name, bases, dct):
        print('[2] MetaClass2.__new__')
        cls = super(MetaClass2, meta).__new__(meta, name, bases, dct)
        return cls

class MetaClass3(MetaClass2):
    def __new__(meta, name, bases, dct):
        print('[3] MetaClass3.__new__')
        cls = super(MetaClass3, meta).__new__(meta, name, bases, dct)
        cls.mem_attr = 'a'
        return cls
```

在example 3-1中，我们让元类MetaClass3创建类Class1。

首先，调用MetaClass3的__new__方法，执行第17行打印"[3] MetaClass3.__new__"。

接下来，进入到第18行super的使用。这里super的第二个参数meta传入的是MetaClass3，它决定了方法的调用者并确定一条MRO链，如example 3-2所示；另外，super的第一个参数是MetaClass3，我们在这条MRO链中找到MetaClass3，并找到它最近的父类，也就是MetaClass2。所以，第18行中super(MetaClass3, meta)调用__new__方法，就是让MetaClass3去调用MetaClass2的__new__方法，这便执行了第11行的打印"[2] MetaClass2.__new__"。

接着，来到第12行super的使用。第二个参数meta传入的依旧是MetaClass3，第一个参数MetaClass2在MRO链上向上寻找到的是MetaClass1。所以，第12行相当于让MetaClass3去调用MetaClass1的__new__方法。然而MetaClass1没有__new__方法，便求助于它的父类type最终完成了类的创建。

最后，在example 3-3中，类Class1创建实例对象obj1，这会调用元类MetaClass3的__call__方法，MetaClass3中没有就向父类中寻找，最后调用MetaClass1的__call__方法。example 3-4显示了我们在元类中（第6行和第19行）"悄悄"添加的类属性和实例属性。

```
# example 3-1
class Class1(metaclass = MetaClass3):
    pass
```

> [3] MetaClass3.__new__
> [2] MetaClass2.__new__

```
# example 3-2
print(MetaClass3.__mro__)
```

> (<class '__main__.MetaClass3'>, <class '__main__.MetaClass2'>, <class '__main__.MetaClass1'>, <class 'type'>, <class 'object'>)

```
# example 3-3
obj1 = Class1()
```

> [1] MetaClass1.__call__

```
# example 3-4
print('[1]', Class1.mem_attr)
print('[2]', obj1.attr)
```

> [1] a
> [2] 1

example 4是一个更加错综复杂的案例，它同时涉及类的继承、元类的继承以及元类创建类。具体来说，

*   元类MetaClass2继承元类MetaClass1，元类MetaClass1和MetaClass2分别创建类Class1和Class2，类Class2继承类Class1；
*   元类MetaClass1中定义了__new_方法，功能是将其创建类的属性名和方法名，只要不是以双下划线开头，均自动转化为小写；元类MetaClass2重写了这个__new__方法，唯一的改动是将"转化为小写"改为"转化为大写"；
*   类Class1和Class2中分别定义了方法Func1和Func2——这两个方法名均为大小写混合。

那么，经过元类加工后，类Class2中的方法名的大小写情况究竟如何？特别地，Func1是先被MetaClass1转化为func1再被Class2继承，还是先被Class2继承再被MetaClass2转化为FUNC1?

答案为"func1"和"FUNC2"。

```
# example 4
class MetaClass1(type):
    def __new__(meta, name, bases, dct):
        upper_dct = {
            k if k.startswith("__") else k.lower(): v
            for k, v in dct.items()
        }
        return type.__new__(meta, name, bases, upper_dct)

class MetaClass2(MetaClass1):
    def __new__(meta, name, bases, dct):
        upper_dct = {
            k if k.startswith("__") else k.upper(): v
            for k, v in dct.items()
        }
        return type.__new__(meta, name, bases, upper_dct)

class Class1(metaclass = MetaClass1):
    def Func1(self):
        pass

class Class2(Class1, metaclass = MetaClass2):
    def Func2(self):
        pass

print([x for x in dir(Class2) if not x.startswith('__')])
```

> ['FUNC2', 'func1']

通过这个案例，我们可以更加深入地了解元类和父类的区别。

一方面，在类的内部所定义的内容会作为"原料"传递给元类进而加工为"产品"。比方说，在类Class2的内部，只有Func2被定义，那么Func2这个方法名和对应的函数就会作为键值对传递给元类MetaClass2，"原料"Func2从而加工成"产品"FUNC2，类Class2因此可以调用FUNC2；而Func1根本就没有在类CLass2的内部定义，也就不会传递给元类MetaClass2。

另一方面，通过继承关系只会拿取父类的"产品"。比方说，Func1在类Class1中定义，因此"原料"Func1传递给元类MetaClass1后加工成"产品"func1；由于类Class2和Class1的继承关系，类Class2自身没有func1但可以直接使用父类Class1的"产品"func1。从这个层面上来看，元类创建类的优先级要高于类继承父类。

## 3\. 小结

本文和上一篇文章囊括了读懂backtrader源码所需要的元类相关知识，包括：

*   元类是创建类的类，type (和它的子类) 是元类；
*   定义类时使用metaclass关键字指定创建类的元类；
*   元类的__new__方法接受哪些参数，以及它如何介入和改变类的创建；
*   元类的__call__方法如何介入和改变创建的类创建实例对象的过程；
*   元类创建类的优先级要高于类继承父类。

有了这些基础知识，从下一篇文章开始，我们将正式开始解读backtrader源码。

原创不易，如果你觉得文章有用，欢迎点赞转发！