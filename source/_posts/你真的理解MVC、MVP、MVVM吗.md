---
title: 你真的理解MVC、MVP、MVVM吗
date: 2018-04-07 22:11:16
tags:
	- MVC
	- MVP
	- MVVM
---

大概是二三十年前， 人类逐渐从命令行界面时代走出来，进化到了GUI时代。

注： GUI（Graphic User Interface），即图形用户接口。

![img](http://mmbiz.qpic.cn/mmbiz_png/KyXfCrME6UJ9L6uFDhE2R8VrblzNrw20nPbZiaJvDpsTZ9bRQ0bQftUicmZ0nlxy512DN0jMlMxf6v2LeW2jgzQw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

（一个命令行程序）

<!--more-->

![img](http://mmbiz.qpic.cn/mmbiz_png/KyXfCrME6UJ9L6uFDhE2R8VrblzNrw20On59EmAo6XatRTahyH9LOJV9IIlqAGAKeTvxRxtnicoiaoc7ibJ7TLlhg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

（一个带有图形界面的桌面应用程序 ，自己画的，有点丑啊）

每当人类努力地开发新的桌面GUI程序的时候， 至少要搞定下面几类工作：

1. 界面（以及界面中元素的）布局。

这是一件挺费劲的工作， 要尽可能地美观漂亮，要不然就卖不出去。

1. 界面上有些“逻辑”需要处理

比如上图中那个薪水计算程序，“计算” 按钮默认是灰色的， 不能点击，用户输入了税前收入以后， “计算”按钮就会被激活，表示计算了。

1. 所谓的业务逻辑。

用户点击了“计算”按钮以后，计算五险一金，个人所得税和税后收入。

这三者搅在一起，让程序代码凌乱不堪，稍微复杂点儿的程序就长达几千行， 不断地挑战着程序员的底线，修改别人的代码，添加新的功能要比从头写难好多倍！

大家都在泥潭中挣扎。

桌面应用程序的 MVC

程序越来越复杂，Bug越来越多，没办法， 大家只好去求编程上帝。

上帝说： 想从困境中走出来，一定要实现关注点分离（Separation of concerns）。

没人能够理解。

上帝解释道：“ 你们人脑同时能处理的东西是有限的， 所以要把一个大系统给分解，变成几个相对独立的部分，这样我们的大脑每次只关注某一方面，暂时忽略其他的，就能够掌控了。”

没人知道该怎么分解。

上帝只好想了一个办法， 把关注点分离的理论给具体化，这个办法就是MVC。

上帝告诉人类：

**M 表示 Model , 专门用来处理业务逻辑，不干别的事情。**

例如在那个薪水计算系统中。计算一个人的薪水，五险一金，个人所得税等等。

**V 表示View， 专注页面布局和数据显示。**

例如把Button放置到某个位置，把总收入显示到一个文本框，把税金显示到另外一个地方。

**C 表示Controller  翻译用户的输入，操作模型和视图。**

例如，用户在界面点击了一个“计算”的按钮，View 把计算的请求传递给Controller (很明显View需要知道Controller，换句话说，需要持有Controller的实例)，Controller找到或者创建Model，执行业务逻辑：计算薪水。

计算的结果该怎么展示呢？  人类问道。

上帝胸有成竹： 可以让Model 去通知View。

Model需要持有View的实例（当然也可以通过观察者模式），调用View对应的方法。

例如： View中可能有一个onResult的方法， 让Model去调用，在调用的时候把一个参数对象Salary传递过来，不就可以展示数据了吗？

```
// View的方法，被Model调用： 
public void onResult(Salary salary){   
      //把个人所得税(salary.getTax()) 展示到一个文本框
      //把净收入(salary.getNetPay()) 展示到另外一个文本框
      ......
}
```

画成流程图的话是这个样子：

![img](http://mmbiz.qpic.cn/mmbiz_png/KyXfCrME6UJ9L6uFDhE2R8VrblzNrw20UF6KIAHev0c0mOqF6ue0iae1c6wia91icbcTS8B06ialobCuch4UoJuficQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

大家都觉得MVC大法好，纷纷开始使用。



### **MVP**

时间久了以后，人类就觉得不爽了，因为在这个MVC中，依赖太多：

View 依赖Controller和Model

Controller依赖View和Model

Model 和View的关系虽然很弱， 但是也需要某种方式来通知View进行数据更新。

人类说：“他们之间的耦合还是挺紧密的啊，亲爱的上帝，能不能改改？”

上帝觉的人类还是挺有上进心的，决定继续施以援手： “这样吧， 可以改变一下Controller， 把Model和View完全隔离开，让他们单独变化。”

![img](http://mmbiz.qpic.cn/mmbiz_png/KyXfCrME6UJ9L6uFDhE2R8VrblzNrw20YxTdxFytrs0ErQmZ4f85UofjGs3eFEATwCMshMs6ARic3AgmfqSicbUg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

上帝把Controller 改了个名称，叫做Presenter， 把整体命名为MVP。

在MVP当中，View只知道Presenter, 不知道Model 。

计算流程和MVC差不多，用户点击了“计算薪水”按钮， View去调用Presenter， Presenter操作Model , Model 中进行业务计算。 关键点是，Presenter去更新View。

```
//Presenter 的方法，被View调用
public void calculateSalary(){
    //调用Model计算薪水
    view.showTax(xxx);    // 调用View显示所得税
    view.showNetPay(xxxx);//  调用View净收入
    ......
}
```

但是Presenter还是需要调用View的方法，也就是说Presenter对View有依赖，这样Presenter就没办法单独做单元测试，非得等到界面做好以后才行。

于是上帝又做了一点改进，让View层提取出接口，Presenter只依赖这个接口。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

这样Presenter不用依赖真正的界面就可以测试了，并且也增加了复用性，只要View实现了那个接口，Presenter就可以大发神威。



### MVVM

使用了一段时间MVP以后，永不满足的人类又觉得不爽了， 因为让Presenter调用View的方法去设置界面，仍然需要大量的、烦人的代码，这实在是一件不舒服的事情。

人类突发奇想： 能不能告诉View一个数据结构，然后View就能根据这个数据结构的变化而自动随之变化呢？

上帝看到人类思考了，表示了赞赏。

他说，我来送你们一个叫做ViewModel的东西，它可以和View层绑定。 ViewModel的变化，View立刻就会变化。

人类问： ViewModel？ 里边有什么东西？

上帝说： 拿你们的薪水计算为例， ViewModel 差不多这样：

```
public class SalaryViewModel{
    String grossSalary;  //税前收入，和View中的相关字段对应
    String netSalary;    //净收入，和View中的相关字段对应
    String tax;          //个人所得税，和View中的相关字段对应
    ...... 
    boolean  isCalculating;  // 一个标志位，表示正在计算 
    String errMsg;           // 如果出错的话，记录出错消息。
}
```

当用户在界面上点击“计算”按钮的时候， 你们需要设置一个SalaryViewModel中的标志位：

salaryViewModel.isCalculating = true;

这样View 中就可以自动给用户展示一个消息：“正在计算....”

当薪水计算完成的时候， 如果没有错误，SalaryViewModel 中grossSalary， netSalary，tax等属性就有了值。 与此同时View 中对应的内容也会更新， 不用你们手工去设置， 很方便吧？

如果计算过程出错， SalaryViewModel 的errMsg 会保存出错消息， 同样，View中会自动把这个错误消息给显示出来， 很智能吧？

人类说：“怎么可能这么智能呢？ 这里的ViewModel 好像和View没有什么关系啊？ 到底该怎么绑定啊？！！！”

上帝笑了： 你们可以开发一个框架嘛？ 让两者绑定起来不就行了？

人类没有办法，只好自己动手。

（注：实际上微软的WPF和Silverlight，  Android等框架和系统都可以实现View和ViewModel之间的映射和绑定）

Web应用程序的MVC

时间过得飞快，人类发明了互联网，Web应用程序如雨后春笋般崛起，B/S(浏览器-服务器)开始大行其道。

用户通过浏览器发出GET,POST请求，服务器端进行处理，处理完以后生成HTML给浏览器。

无论什么操作，都是对服务器端URL的访问。

人类突然发现，整个编程模型发生了巨变， 不能简单地套用原来的MVC和MVP了。

如果把HTML页面比作原来桌面应用程序的View， 服务器无论是Controller还是Model都是无法远程遥控这个View进行处理的。

人类这一次没有去请教上帝，自己尝试对MVC进行改良，其中有个叫Rod Johnson 带领一帮人搞出的SpringMVC很成功。

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

**不像桌面应用的MVC， 这里的Model没法给View 发通知。**

**也不像MVP, 这里的Controller 也不会调用View的方法来设置界面。**

实际上Controller 会选择一个View， 然后把模型数据“丢过去”渲染。

原来的View 变成了 View Template（例如JSP , Velocity等等）， 经过渲染后变成HTML发给浏览器展示给用户。

有人把这种MVC称为 基于Web的 MVC，以便和之前的MVC区别开来。

前后端的分离

人类早期的B/S应用程序中， 每次访问服务器端， HTML就会整体发给浏览器，即所谓的整体刷新。

后来人类发明了AJAX， 可以做到局部刷新。

于是浏览器端的应用变得越来越复杂，再后来人类竟然发明了Web上的SPA(单页应用程序)，用起来的体验和最初的桌面应用程序越来越像。

人类发现，那些MVC, MVVM之类的模式完全可以用到浏览器端嘛！

例如在浏览器端使用MVVM , 在服务器端可以使用MVC， 两者结合起来：

![img](http://mmbiz.qpic.cn/mmbiz_png/KyXfCrME6UJ9L6uFDhE2R8VrblzNrw20Q5cAKn82lN9xLDyiaJZlIvmQib6TY7n0p5zlNU6VBvgyvZL6OMTiaauPw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

![img](http://mmbiz.qpic.cn/mmbiz_png/KyXfCrME6UJ9L6uFDhE2R8VrblzNrw20mVhNBPiay38bCYU2KjezOFPj35vCp5Avl1EP1TtKfSJyOAIYzWSESHA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

前端和后端成功地分家了 ！