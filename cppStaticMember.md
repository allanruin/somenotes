#C++的const static相关问题

##0x00 楔子
  此段讲述写下此篇笔记的背景故事，可跳过。在微博上看到@睡眼惺忪的小叶先森 的一条微博，谈到const static的class member
  的问题，觉得似曾相识，打算开电脑后研究一下，然后等开了电脑微博已经删了，一会叶先森已经把总结的文章都发出来了。╮(╯-╰)╭，
  自己也探寻了一下这个问题，是以为记。
    
##0x01 问题代码
下面是叶先生提的代码问题

````
void f(const int &value)
{
}
class Test
{
public:
  static const int a = 1;
};
int main()
{
  f(Test::a);
  return 0;
}
````

上面的代码在Visual Studio 2013通过，但在gcc上报错“Undefined reference to ‘Test::a’”，为什么会这样呢？
更有甚者，为何下面的又ok呢？

```
void f(const int value) //这里没有 &
{
}
class Test
{
public:
  static const int a = 1;
};
int main()
{
  f(Test::a);//没问题
  return 0;
}
```

##0x02 C++的static关键字
C++中的static 关键字在类中，声明类的成员（包括数据成员和函数成员）不绑定于特定的类实例。

一个类的static数据成员不与这个类的对象（实例）关联，具有静态存储期（static storage duration）。一个类的static函数成员
其实就是一个普通的在定义域内之定义一次的函数。static关键字只在静态成员的声明（declaration）时使用，在静态成员的定义
(definition)时不使用。

这里关键来了，一个极其容易误会的地方就是静态成员的生命与定义的区别。我们直觉上觉得我TM在一个class里写`static int a=1;`
不就是声明并定义顺便赋初值了么。但是很遗憾，并不是这样的。C++中规定，**在类里的static成员的声明并非定义**。比如
`class X { static int n; }; `这里的n是申明，要定义n，你得再加一句这样的`int X::n = 1;`

当你在类里声明了static member，则这个static成员和包含它的类就变成了一个类似于void的不完整类型（incomplete type）

到这里我们其实已经差不多回答了第一个问题，然而事实是并没有。

##0x03 odr-used

好了，晚上1：21了，我要睡觉了，明天上排队论要提前一个小时抢位置。

