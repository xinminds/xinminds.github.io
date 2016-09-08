# python 下划开头变量的用法

经常看到 python 代码中有用下划线开头的变量，一直以为是特殊变量才这样命名；又者在某次 python 讲座里听到过下划线开头的变量 python 的解析机制不一样；众说纷纭，所以对这种变量总是敬而远之。后来工作中越来越频繁地使用 python，感觉不能再这样糊涂下去了，因此特意去查明白下划线开头的变量有什么特殊之处。

幸好 python 很多东西都可以引经据典，不用怕无法考究。所有种种在 pep8 中都有说明。

## 单下划线变量

pep8 原文:

```
In addition, the following special forms using leading or trailing underscores are recognized (these can generally be combined with any case convention):

_single_leading_underscore : weak "internal use" indicator. E.g. from M import * does not import objects whose name starts with an underscore.

single_trailing_underscore_ : used by convention to avoid conflicts with Python keyword

```

翻译过来也就是:

1. 对于单下划线开头的变量，可以认为是较“弱”的内部使用的变量，在 from M import * 时是不会 import 模块 M 中以单下划线开关的变量的。
2. 对于单下划线结尾的变量，用于避免与关键字冲突而使用的机制，例如 Tkinter.Toplevel(master, class_='ClassName') 中的 class_。


## 双下划线变量

pep8 原文:

```
__double_leading_underscore : when naming a class attribute, invokes name mangling (inside class FooBar, __boo becomes _FooBar__boo ; see below).

__double_leading_and_trailing_underscore__ : "magic" objects or attributes that live in user-controlled namespaces. E.g. __init__ , __import__ or __file__ . Never invent such names; only use them as documented.
```

翻译过来就是:

1. 对于在类中使用双下划线开头的变量，python 会对其进行名字修改。例如 class FooBar, __boo 会被改成 _FooBar__boo。
2. 对于开头与结尾均有下划线的变量，这是一些 "magic" 对象或属性并且存在于用户控制的命名空间中。千万不要随意使用。
