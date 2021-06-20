---
title: Kotlin 动态代理学习与实践
date: 2021-06-20 22:57:56
tags: Android World
---

>最近接了个大项目，哈哈哈~ 文章终于出来啦~ 还好没胎死腹中。最近的感悟就是，做好小事，才有机会做大事，承担大项目！   
这次的文章本来想写 Kotlin 学习笔记2，结果写着写着发现动态代理这块之前还是没搞明白，所以就学了下 Kotlin 中的代理模式写法，发现东西有点多，遂独自成文，欢迎大家拍砖！

## 1. 引子
动态代理主要是用来干什么的？通俗一点，就是你在调用其他类的一些方法时，想加入你自己的一些处理逻辑。比如说，统计这些方法的执行时长等，这也是面向切面编程的思想。    

## 2. 代理模式
动态代理，源自于设计模式中常见的一种模式：代理模式。在 Java 中就是为一个对象 A 的一个方法 B 提供一个代理对象，这个代理对象可以完全控制 A 对象的 B 方法实际的执行内容。四个关键词：1）代理对象；2）被代理对象；3）被代理行为；4）对行为的完全控制。

这样说还是太抽象，举个实际的例子。假如我们需要通过房屋中介租房，就是一个简单的代理模式。这里面有三种角色：1）房东——被代理对象；2）房屋中介——代理对象；3）租客——使用方或调用者。房东把全部的租房事项全部交给中介打理，那么中介就具有对租房行为的完全控制，租客只能跟中介打交道。

看看上面的四个关键词，中介 对应 **代理对象**；房东 对应 **被代理对象**；租房相关事项 对应 **被代理行为**，比如找租客、约看房时间、看房、签约等；中介可以完全控制对租房事项的行为 对应 **对行为的完全控制**，比如中介可以加价，提供保洁服务等。   

### 2.1 代理模式的基本要素
1. 代理对象与被代理对象需要实现相同的接口。即对于出租房屋来说，就是出租房屋的相关事项，房东和中介都必须完成的出租流程手续。
2. 代理对象中有被代理对象的引用，这样外部调用者在调用代理对象的方法时，代理对象就会在内部交给被代理对象去实际执行。即，中介可以代替房东去出租房屋，最终决定权还是在房东手里。
3. 外部调用者是直接使用接口进行调用的，对被代理对象的信息有一定的保护作用。即，租客只能通过出租房屋的流程手续与中介进行沟通，并不知道房东的个人信息。

### 2.2 租房代码实例 - Java 版
Talk is cheap，Show me your Code! 我们具体来看看代码怎么表示。
首先是租房的一些流程，也就是被代理的行为，这里为方便只写了三个步骤。通常用抽象类或接口的形式表示：
```
// code 1
interface IRentHouse {
    // 带领租客看房
    void visitHouse();

    // 讨价还价
    void argueRent(int rent);
    
    // 签合同
    void signAgreement();
}
```
其次，房东的实现，也就是被代理对象的行为，房东的权利。
```
// code 2
class HouseOwner implements IRentHouse{
    @Override
    public void visitHouse() {
        System.out.println("HouseOwner 带领看房，介绍房屋特点");
    }

    @Override
    public void argueRent(int rent) {
        System.out.println("HouseOwner 提出租金为：" + rent);
    }

    @Override
    public void signAgreement() {
        System.out.println("HouseOwner 签合同");
    }
}
```
接下来就是房屋中介的实现，因为中介得到了房东的授权，所以他可以有房东的所有权利，他代理房东去操作出租房屋的相关事项。因此，中介会先征求房东的同意，拿到授权，在代码中的表现就是持有房东的引用。
```
// code 3
public class HouseAgent implements IRentHouse{
    IRentHouse mHouseOwner;    // 中介持有房东的权利

    public HouseAgent(IRentHouse houseOwner) {
        mHouseOwner = houseOwner;
    }

    @Override
    public void visitHouse() {
        mHouseOwner.visitHouse();
    }

    @Override
    public void argueRent(int rent) {
        mHouseOwner.argueRent(rent);
    }

    @Override
    public void signAgreement() {
        mHouseOwner.signAgreement();
    }
}
```
在 main 方法中就可以如下调用了：
```  
// code 4
HouseAgent agent = new HouseAgent(new HouseOwner());    // 传入房东对象，拿到授权
agent.visitHouse();
agent.argueRent(300);
agent.signAgreement();
```
这其实就是一个静态代理的例子。为啥是“静态”？因为这里的代理方法是写死的，如果 HouseOwner 对象新增方法，那么在 HouseAgent 和 接口中都得新增代码，不够灵活。

那么代理模式有什么作用？看起来好像就是代理类对被代理类做了一层封装而已，其实这层封装在一定程度上保护了被代理类的信息，使用者不用关心内部的具体实现。此外，代理类可以根据自身特点和客户需求进行功能扩展，在保证 HouseOwner 类的核心方法正确执行的情况下，扩展其他的行为。例如：租户刚毕业社会经验不足，遇到了一个黑心中介，需要在你看房前给小费：
```
// code 5
public class HouseAgent implements IRentHouse{
    IRentHouse mHouseOwner;    // 中介持有房东的权利
    int mTip = 0;    // 小费

    public HouseAgent(IRentHouse houseOwner) {
        mHouseOwner = houseOwner;
    }

    @Override
    public void visitHouse() {
        if (mTip > 10) {
            mHouseOwner.visitHouse();
        } else {
            System.out.println("小费不够，暂不能看房");
        }
    }

    @Override
    public void argueRent(int rent) {
        mHouseOwner.argueRent(rent);
    }

    @Override
    public void signAgreement() {
        mHouseOwner.signAgreement();
    }

    public void giveTip(int tip) {
        mTip = tip;
    }
}
```

### 2.3 租房代码实例 - Kotlin 版
上面静态代理的 Java 代码写起来虽然容易，但是套路都一样，显得繁琐。Kotlin 代码中，使用 by 关键字就可以了，非常方便，还是上面的例子，Kotlin 代码为：
```
// code 6
interface IRentHouse {
    // 带领租客看房
    fun visitHouse()

    // 讨价还价
    fun argueRent(rent: Int)

    // 签合同
    fun signAgreement()
}

class HouseOwner: IRentHouse {
    override fun visitHouse() {
        println("带领看房，介绍房屋特点")
    }

    override fun argueRent(rent: Int) {
        println("提出租金为：$rent")
    }

    override fun signAgreement() {
        println("签合同")
    }
}

class HouseAgent(houseOwner: IRentHouse): IRentHouse by houseOwner {}

// 调用的代码如下
HouseAgent(HouseOwner()).visitHouse()
HouseAgent(HouseOwner()).argueRent(500)
HouseAgent(HouseOwner()).signAgreement()
```
一个 by 关键字，就避免写很多重复的代码了。如果代理类要在原有的基础上添加自己的方法或功能，只需要在代理类中重写被代理对象中的方法。还是拿上面的例子说明：
```
// code 7
class HouseAgent(houseOwner: IRentHouse): IRentHouse by houseOwner {
    var mTip = 0
    private val mHouseOwner = houseOwner

    fun giveTip(tip: Int){
        mTip = tip
    }

    override fun visitHouse() {
        if (mTip > 10) {
            mHouseOwner.visitHouse()
        } else {
            println("小费不够，暂不能看房")
        }
    }
}
```

所以，静态代理就是把代理关系的代码是写死的，若被代理对象添加了新的方法，那么代理类和接口都需要改动。当然如果是用 Kotlin 写，那么只需要对接口进行改动。但是，如果我们想要切面编程，在每个被代理对象的方法中添加自己的处理，比如，我需要知道每个方法步骤的执行用时，那么就得在每个方法调用前后记录时间戳，这就很繁琐了。而且每新增方法都得重复写相同的代码，这种情况下，就需要使用动态代理。

## 3. Java 动态代理实现
创建 Java 的动态代理需要使用 Proxy 类：
```
// code 8
java.lang.reflect.Proxy
```
调用它的 newProxyInstance 方法，就可以为某类创建一个代理类。例如给 Map 创建一个代理类：
```
// code 9
Map mapProxy = (Map) Proxy.newProxyInstance(
        HashMap.class.getClassLoader(),
        new Class[]{Map.class},
        new InvocationHandler() {
            @Override
            public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                return null;
            }
        }
);
```
可以分析下这个方法，方法签名为：
```
// code 10
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
```
1. ClassLoader 类型的 loader 对象：被代理类的类加载器；
2. Class 数组的 interfaces 对象：被代理的接口，对应于之前关键词中的 被代理的行为，这里传入的是接口而不是某个具体的类，所以表示行为；
3. InvocationHandler 接口对象 h：代理的具体的行为，对应之前关键词中的 对行为的完全控制，这也是 Java 动态代理的核心；
4. 返回的 Object 对象：对应之前关键词中的 代理对象。

所以，要实现一个动态代理大概需要下面几个步骤：

一、定义要被代理的行为
例如 code1 就是定义的要被代理的行为。 

二、定义被代理的对象
就是实现了接口的类，例如实现了租房流程的房东，详见 code2。

三、建立代理关系
这个是代理的核心，需要一个实现了 InvocationHandler 接口的类：

```
// code 11
public class AgentHandler implements InvocationHandler {

    private Object target;
    public AgentHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("方法执行前");
        Object result = method.invoke(target, args);
        System.out.println("方法执行后");
        return result;
    }
}
```
接口中的 invoke 方法比较重要。方法体中的 Object 类型的 proxy 是最终生成的**代理对象**；Method 类型的 method 是被代理的方法；Object[] 数组类型的 args 是被代理方法执行所需要的参数。target 对象就是传入的代理类。在这个类中，可以在 invoke 方法前后插入我们需要执行的代码，这样做可以使得被代理类对象在执行任何方法时，都会执行我们插入的代码。例如，我们可以在 invoke 方法前后记录下时间戳，这样就可以得出被代理类对象执行的每一个方法的执行时长。    
    
然后代理类利用 InvocationHandler 进行代理：
```
// code 12
public class HouseAgentSmart {
    private IRentHouse mHouseOwner;    // 房东，被代理类对象
    public HouseAgentSmart(IRentHouse houseOwner) {
        mHouseOwner = houseOwner;
        mHouseOwner = (IRentHouse) Proxy.newProxyInstance(
                houseOwner.getClass().getClassLoader(),
                new Class[]{IRentHouse.class},
                new AgentHandler(mHouseOwner)    // 将被代理对象传入
        );
    }

    public IRentHouse getAccess() {    // 用于返回代理对象
        return mHouseOwner;
    }
}
```

四、执行者调用
```
// code 13
IRentHouse smartAgent = new HouseAgentSmart(new HouseOwner()).getAccess();
        smartAgent.visitHouse();
        smartAgent.argueRent(400);
        smartAgent.signAgreement();
```

## 4. Kotlin 动态代理实现
上面是动态代理的 Java 实现，那么如何用 Kotlin 实现？
其实是一样的，只不过是编程语言的语法不同
```
// code 14
// IRentHouseKT.kt    1、首先还是定义接口
interface IRentHouseKT {
    // 带领租客看房
    fun visitHouse()

    // 讨价还价
    fun argueRent(rent : Int)

    // 签合同
    fun signAgreement()
}

// HouseOwnerKT.kt    2、然后是被代理类，实现接口
class HouseOwnerKT : IRentHouseKT {
    override fun visitHouse() {
        println("HouseOwner 带领看房，介绍房屋特点")
    }

    override fun argueRent(rent: Int) {
        println("HouseOwner 提出租金为：$rent")
    }

    override fun signAgreement() {
        println("HouseOwner 签合同")
    }
}

// AgentHandlerKT.kt    3、建立代理关系
class AgentHandlerKT : InvocationHandler {

    private var mTarget : Any? = null
    constructor(target : Any?) {
        this.mTarget = target
    }

    override fun invoke(proxy: Any?, method: Method, args: Array<out Any>?): Any? {
        println("方法执行前")
        // 因为传来的参数 args 是不确定的，所以用 *args.orEmpty() 传参
        val result = method.invoke(mTarget, *args.orEmpty())
        println("方法执行后")
        return result
    }
}

// HouseAgentSmartKT.kt    4、代理类，通过 AgentHandlerKT 得到代理关系
class HouseAgentSmartKT {
    var mHouseOwner : IRentHouseKT? = null

    constructor(houseOwner : IRentHouseKT) {
        mHouseOwner = houseOwner
        mHouseOwner = Proxy.newProxyInstance(
                houseOwner.javaClass.classLoader,
                arrayOf(IRentHouseKT::class.java),
                AgentHandlerKT(mHouseOwner)
        ) as IRentHouseKT
    }
}

// 调用方进行调用
val smartAgent = HouseAgentSmartKT(HouseOwnerKT()).mHouseOwner
smartAgent?.visitHouse()
smartAgent?.argueRent(500)
smartAgent?.signAgreement()
```

看到这里就有人要问了，咦？之前不是用 by 关键字就可以在 kotlin 中进行代理吗？为啥还需要像 Java 一样用 Proxy.newProxyInstance() 方法写代理的模式？这两种方式有什么区别？

首先，这两种方式都可以在 Kotlin 中实现代理模式，但适用的场景有所不同。
1、by 关键字实现的代理模式。使用起来更加方便，代理的粒度更小，可以根据代理类自身需要对某些被代理类中的方法进行重写；
2、Proxy.newProxyInstance() 方法实现的代理模式。实现比较繁琐，代理的粒度大，一旦代理，就是代理所有的方法。可以在原方法前后插入自己想要执行的代码，适用于埋点记录 log 日志、记录方法执行用时等。

# 参考文献
1. [Java动态代理——框架中的应用场景和基本原理](https://www.cnblogs.com/tera/p/13911819.html)
2. [当Kotlin邂逅设计模式之代理模式(二)](https://zhuanlan.zhihu.com/p/66548420)      

ps. 赠人玫瑰，手留余香。欢迎转发分享，你的认可是我继续创作的精神源泉。    

还没看够？来我的公众号看看？搜索： **俢之竹**   
或者扫一扫下面的二维码：  

![俢之竹公众号](5-Kotlin-Dynamic-agent-Learning-and-Practice/pic1.jpg)   

来知乎逛逛也是可以的，搜索： **俢之竹**   

