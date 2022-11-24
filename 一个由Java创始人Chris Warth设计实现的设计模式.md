# 一个由Java创始人Chris Warth设计实现的设计模式

一个由Java创始人**Chris Warth**设计实现的设计模式；一个浪费了电商平台海量算力资源和网络资源的设计模式；一个为电商平台立下汗马功劳的设计模式;一个在JDK1.0就已经被全力支持，JDK9又被无情抛弃的设计模式；一个拥有多个化身的设计模式。

------

大家好，极客架构师——专注架构师成长，我是码农老吴。

本期是《架构师基本功之设计模式》**第16期。**

在第15期，我给大家分享了，行为型设计模式中的迭代器设计模式（Iterator design pattern），并对JDK框架集合中的ArrayList，HashMap中的迭代器进行了源码分析。

本期，我将分享一个命运多舛，坚韧不拔的设计模式，它的发展历程源远流长：

**C/S时代：**发迹于GUI项目，

**B/S时代：**隐身于MVC模式，

**RPC时代：**大器晚成，修成正果，成为了消息系统的灵魂。

![img](./observer/80cf2ce3c4014dcb8781226df26f5a96~noop.image)



# 结论先行

基于REIS分析模型，观察者模式由四种角色组成，分别是观察者角色（Observer Role），被观察者角色（Obserable Role），消息角色（Message role），以及缓冲池角色（cache pool role），它的宗旨是，通过缓冲池角色，实现观察者角色与被观察者角色的解耦合。

# 直播通知

后面我分享的相关视频，一般会首先在直播中分享，希望大家多多捧场。

本平台的直播时间，根据大家的投票结果，暂定为每周六晚上，具体时间待定。



# 分享思路

题外话：从埃隆马斯克(**Elon Musk)**吐槽RPC说起

功臣还是罪人？

案例介绍：订单流转

第一版代码：无设计模式

第二版代码：基于观察者模式

观察者模式定义解析

第三版代码：基于JDK内置的观察者模式模块

REIS分析模式解析观察者模式

观察者模式的化身

通用代码和类图

下期预告



# 从埃隆马斯克(Elon Musk)吐槽RPC说起

![img](./observer/6344032cc8d14199802d3e385b0cf4b8~noop.image)



最近，美国硅谷的码农们正处于水深火热之中，Twitter的员工更是如履薄冰，战战兢兢。

科技狂人埃隆马斯克，新官上任三把火，先是一口气裁员半数以上，接着发生马斯克吐槽twitter APP 滥用**RPC**技术，造成系统运行速度慢，被心直口快，口无遮拦的技术人员回怼（我估计是这几个码农的印度裔领导被开除，没人管造成的），老马发扬自己霸道总裁的作风，直接开除了怼他的员工的狗血剧情。

从马斯克的嘴里，能说出RPC，说明他确实是技术出身，而且抓住了这个时代，软件开发的核心技术点。我认为，构成当今分布式微服务架构，至少有两块基石，那就是RPC和异步消息系统。RPC框架，我在后面的微服务架构系列专题中，会给大家分享。今天我们只聊异步消息系统背后的设计模式。

![img](./observer/14d0f491b3e04efeb1baa28dd51fcc72~noop.image)

谁敢怼我？

# 功臣还是罪人？

当你在电商平台，享受购物的乐趣时，是否想过，你的每个订单，电商平台的后台系统，会发生多少次运算（CPU算力资源）和网络传输（网络资源）呢？

1次，10次，100次，1000次，还是10000次。

在京东这样规模的电商平台，答案可能惊掉你的下巴。

仅仅是订单信息的网络传输次数，就还需要再增加一个数量级，也就是以10万为单位来计算，而对订单进行业务校验的计算次数，则还需要根据购物车的商品数量，用网络传输次数再乘一个系数，数量之大超乎大家的想象。

更离谱的是，这么多次的运算和网络传输，有多少是无效的呢？

10%，30%，50%，60%，80%，

不对，都不对，还是太少，无效的数量往往在90%以上，极端情况，无限地接近100%。

是不是危言耸听，哗众取宠呢，还是电商平台的架构师都是吃干饭的呢？

一切的根源，都和我们今天要分享的设计模式，有很深的渊源。

当用户在电商平台下单后，电商平台的订单中台，会给用户生成相应的订单，用户在自己的PC或者APP的订单管理模块，就可以看到自己的订单了。

一个订单，一般包含多个SKU（也就是商品），当用户支付成功后，不同的SKU，要进行不同的后续处理。

![img](./observer/fbf54cda3d504b29bcd21406c5fe72ad~noop.image)



假如你购买的是手机号，运营商垂直业务部门的后台系统，就会先判断订单，是否是自己的订单，如果是，则根据用户的订单，在业务部门的系统中，生成相应的业务订单，并外调运营商（如中国移动或者中国联通）的BOSS系统，在运营商侧生成相应的订单。

同样的，如果你购买的是机票，负责机票旅行的垂直业务部门，也会先判断是否是自己的订单，如果是，则在自己的业务系统，生成相应的业务订单，并外调相应的外部系统，产生机票订单。

另外，电商平台各个业务部门，经常会举办成千上万的各种营销活动，每个活动都会涉及一部分SKU,这些SKU在用户下单并支付成功后，都需要进行一些额外的后续处理。

而作为订单中台，是很难知道，一个订单都需要哪些业务系统进行后续处理的，那怎么办呢？

这种业务场景，非消息系统莫属了。

一方面，订单中台在用户支付成功后，发布订单支付成功的消息，而另一方面，垂直业务部门的业务系统，如果需要关注订单支付成功的消息，则在消息系统对这个消息进行注册。这样就意味着，有多少个业务系统注册了订单支付成功的消息，订单信息在消息系统中，就需要发送多少次。同时，无论前台生成多少订单，垂直业务部门的系统，都需要在接到全量订单的消息后，判断是否需要处理这个订单里面的商品，而大部分情况下，订单对具体的一个业务系统而言，都是无效的，这就是为什么，会造成海量的资源浪费的原因。

对消息系统，有一定了解的朋友，可能都听说过，消息系统的背后，是生产者和消费者模式，还有发布和订阅模式，那么这和我们今天分享的观察者设计模式，有什么关系呢，听完后面的讲解，大家就会一清二楚。

![img](./observer/2039ad656ab44192be4166a3b2339ac1~noop.image)



# 案例介绍：订单流转

我们现在就针对上面的订单处理流程，开发相应的模块来实现。里面有两个模块，一个是订单中台的订单服务，用来支持用户下单，支付等操作。另一个是垂直业务模块，我们假设有两个垂直业务部门，手机业务部门和图书业务部门，对订单进行各自的后续处理。

# 第一版代码：无设计模式

# 类图

![img](./observer/d4fbfcfaa9ff4f0298695bd42a7e0145~noop.image)



**接口及类：**

IOrderService：订单服务接口

OrderService：订单服务实现类

IBusinessService：垂直业务服务接口

BookBusinessService：图书业务服务实现类

MobileBusinessService：手机业务服务实现类

OrderInfo：实体类，订单信息

# IOrderService：订单服务接口

```
package com.geekarchitect.patterns.observer.demo01;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
public interface IOrderService {
    void pay(OrderInfo orderInfo);
}
```

# OrderService：订单服务实现类

这个类里面的pay方法是关键，当用户支付成功后，订单中台，调用各个垂直业务部门的服务，进行后续处理。

```
package com.geekarchitect.patterns.observer.demo01;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
@Service
public class OrderService implements IOrderService {
    private static final Logger LOG = LoggerFactory.getLogger(OrderService.class);
    @Resource()
    private IBusinessService mobileBusinessService;
    @Resource()
    private IBusinessService bookBusinessService;

    @Override
    public void pay(OrderInfo orderInfo) {
        LOG.info("订单中台：订单支付成功");
        LOG.info("订单中台：逐个调用垂直业务部门的服务");
        mobileBusinessService.doService(orderInfo);
        bookBusinessService.doService(orderInfo);
    }
}
```

# IBusinessService：垂直业务服务接口

```
package com.geekarchitect.patterns.observer.demo01;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
public interface IBusinessService {
    void doService(OrderInfo orderInfo);
}
```

# BookBusinessService：图书业务服务实现类

```
package com.geekarchitect.patterns.observer.demo01;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

/**
 * 手机业务服务
 *
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
@Component
public class BookBusinessService implements IBusinessService {
    private static final Logger LOG = LoggerFactory.getLogger(BookBusinessService.class);

    @Override
    public void doService(OrderInfo orderInfo) {
        LOG.info("垂直业务部门：图书业务服务");
    }
}
```

# OrderInfo：实体类，订单信息

```
package com.geekarchitect.patterns.observer.demo01;

import lombok.Data;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
@Data
public class OrderInfo {
    private Long ID;
    private Long MemberID;
    private int orderType;
}
```

# 测试类

```
package com.geekarchitect.patterns.observer.demo01;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class OrderServiceTest {
    static final Logger LOG = LoggerFactory.getLogger(OrderServiceTest.class);
    @Autowired
    private IOrderService orderService;

    @BeforeEach
    void setUp() {
    }

    @AfterEach
    void tearDown() {
    }

    @Test
    void pay() {
        OrderInfo orderInfo=new OrderInfo();
        orderInfo.setID(1L);
        orderInfo.setMemberID(1L);
        orderInfo.setOrderType(100);
        LOG.info("第一版代码：无设计模式");
        orderService.pay(orderInfo);
    }
}
```

# 运行结果

![img](./observer/b6688e5b3ef94304ab027b801694c830~noop.image)



# 案例解析

这版代码没有采用设计模式，仅仅考虑如何完成现有的业务功能，当有新的垂直业务部门增加时，需要在订单服务类中（OrderService）硬编码，增加对新业务的支持。

下面我们采用观察者模式进行代码重构。

# 第二版代码：基于观察者模式

# 类图

![img](./observer/3e531c76d5d7486d87de1aa66db71642~noop.image)



**新增接口和类：**

IOrderServiceV2：被观察者角色，订单服务接口

OrderServiceV2：被观察者角色，订单服务实现类

IBusinessService：观察者角色，垂直业务服务接口（第一版代码）

BookBusinessService：观察者角色，图书业务服务实现类（第一版代码）

MobileBusinessService：观察者角色，手机业务服务实现类（第一版代码）



# IOrderServiceV2：订单服务接口

为了动态增加垂直业务，这个接口增加了三个方法，用于增加，删除和通知垂直业务部门的实现类。

```
package com.geekarchitect.patterns.observer.demo02;

import com.geekarchitect.patterns.observer.demo01.IBusinessService;
import com.geekarchitect.patterns.observer.demo01.OrderInfo;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
public interface IOrderServiceV2 {
    void pay(OrderInfo orderInfo);

    void addBusinessService(IBusinessService businessService);

    void removeBusinessService(IBusinessService businessService);

    void notifyAllBusinessService(OrderInfo orderInfo);
}
```

# OrderServiceV2：订单服务实现类

这个类的**businessServiceList属性，**它在观察者模式中，属于缓冲池角色。在简易的观察者模式中，缓冲池角色，通常由被观察者角色来管理。这个缓冲池用来存储当前被观察者对应的所有观察者角色对象。

```
package com.geekarchitect.patterns.observer.demo02;

import com.geekarchitect.patterns.observer.demo01.BookBusinessService;
import com.geekarchitect.patterns.observer.demo01.IBusinessService;
import com.geekarchitect.patterns.observer.demo01.MobileBusinessService;
import com.geekarchitect.patterns.observer.demo01.OrderInfo;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
@Service
public class OrderServiceV2 implements IOrderServiceV2, InitializingBean {
    private static final Logger LOG = LoggerFactory.getLogger(OrderServiceV2.class);
    private final List<IBusinessService> businessServiceList = new ArrayList<>();

    @Override
    public void pay(OrderInfo orderInfo) {
        LOG.info("订单中台：订单支付成功");
        this.notifyAllBusinessService(orderInfo);
    }

    @Override
    public void addBusinessService(IBusinessService businessService) {
        businessServiceList.add(businessService);
    }

    @Override
    public void removeBusinessService(IBusinessService businessService) {
        //
    }

    @Override
    public void notifyAllBusinessService(OrderInfo orderInfo) {
        LOG.info("订单中台：迭代通知所有的垂直业务部门");
        businessServiceList.stream().forEach(businessService -> businessService.doService(orderInfo));
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        LOG.info("订单中台：初始化所有的垂直业务部门");
        this.addBusinessService(new BookBusinessService());
        this.addBusinessService(new MobileBusinessService());
    }
}
```

# 运行结果

![img](./observer/2b54739e318a402fb84f7b96ab058aa6~noop.image)



# 案例解析

这版代码，我们基于观察者模式对第一版代码进行了重构，主要是在订单服务类中，增加了存储垂直业务部门的集合，以及增加，删除和通知垂直业务部门的方法。这样当垂直业务部门发生变化时，可以方便的增加和删除相应的部门，扩展性得到了提高。

那么这版代码有没有其他问题呢，如果仅仅从观察者模式的常规实现看，应该是可以了，但是，其实这版代码，是有问题的，而且问题还非常严重，在多线程环境下，会发生消息重复，漏发等问题。

待会对比第三版代码，大家就能意识到问题的严重性。

下面我们先了解一下观察者模式的概念。

# 观察者模式定义解析

> Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.
>
> —— Gof《Design Patterns: Elements of Reusable Object-Oriented Software》
>
> 定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。
>
> ——Gof《设计模式：可复用面向对象软件的基础》



观察者模式的定义，我认为定义的比较好，翻译的也比较好，通俗易懂，没啥弯弯绕的东东，也没有陌生的术语。如果非要说它里面的关键词，我认为是**“关系”和“变化”**。

**关系**

什么关系？依赖关系。

依赖关系在观察者模式的重要程度，还体现在，观察者设计模式还有另外一个别名（观察者模式有好几个名字，我们后面都会提到），就叫“Dependents”。

什么是依赖关系？好像码农都知道吧。

就好像儿子和父亲的关系，儿子和父亲之间就是依赖关系，儿子是依赖方，父亲是被依赖方，儿子在成年之前，不能独立生活，需要父亲的抚养。（哦，我只有一个女儿，举女儿和父亲的关系更好一些，但是我感觉,依赖关系是反着的，小家伙刚刚会跑，一天不见，我就感觉，杯子里的咖啡没有了滋味，两天不见，电脑旁绿植的颜色，就淡了，三天不见，脑袋里一片空白，索性赋诗一首）。

# 码农的女儿情-你就是我的世界

> 未有你时，以为拥有整个世界。
>
> 走向产房，害怕失去整个世界。
>
> 捧你在怀，方知你才是——我的整个世界。
>
> 
>
> 一日不见你，杯子中Java的苦味，淡了。
>
> 两日不见你，电脑旁Oak的颜色，褪了。
>
> 三日不见你，脑袋里Java的技巧，忘了。
>
> 
>
> 只要能看你，哪怕一分秒，
>
> 我愿接受千百行代码bug的洗礼。
>
> 只要能抱你，哪怕一厘秒，
>
> 我愿承受万千次系统上线的失利。
>
> 只要能亲你，哪怕一毫秒，
>
> 我愿遭受亿万个黑客的攻击。
>
> 你，就是我的整个世界。
>
> ——吴念，2022年11月22日

（刚开始学写诗，写的不好，请大家见谅，另外，我虽然天天喝美式咖啡，但是没有喝过Java咖啡，我的电脑旁也没有oak树，只是因为它们都和我们的Java语言有关。）

![img](./observer/12fdaeb37ff240e9987d1b44532f60cf~noop.image)



对于依赖关系，最重要的是搞清楚，谁是依赖方，谁是被依赖方，在没有解耦合的依赖关系中，依赖方不能独立存在，被依赖方则反之。

所有研究依赖关系的设计模式，宗旨都包括解耦合依赖双方，干掉依赖关系，或者降低依赖度。

从面向对象的角度看，对象和对象之间，一个对象调用了另外一个对象的方法，或者访问了另外一个对象的属性，这两个对象之间就存在依赖关系。

怎么样的依赖关系？

再给依赖关系加个定语，“一对多”的依赖关系

什么是“一对多”我就不多说了，这里面要强调的，就是一对多里面的“一”，往往是被依赖方，“多”则是依赖方。



**变化**

谁的变化？对象状态的变化，也就是对象属性的变化。

被依赖方对象状态的变化，变化的后果呢，就是所有依赖于它的对象，都会得到通知，并自动更新。



总结一下，观察者模式，就是定义对象之间的依赖关系，当被依赖方（也就是“一”方）状态发生变化时，所有依赖方（也就是“多”方），都会得到通知并被自动更新。

这个定义后面几个字“并被自动更新”，不太严谨，依赖方都会得到通知一般情况下是没问题的，但是，是否被自动更新就不得而知了，是否需要处理这个通知，这个要看依赖方的业务需求。

# 第三版代码：基于JDK内置的观察者模式

# JDK中的观察者模式

JDK对于观察者模式，可谓非常重视，从JDK1.0开始就提供了支持，是由Java创始人之一的**Chris Warth**设计实现的。

![img](./observer/5d6041099e76484d9b957911c80ac7d3~noop.image)



From left to right they are: Al Frazier, Joe Palrang, Mike Sheridan, Ed Frank, Don Jackson, Faye Baxter, Patrick Naughton, Chris Warth, James Gosling, Bob Weisblatt, David Lavallee and Jon Payne. Missing in action: Cindy Long, Chuck Clanton, Sheueling Chang and Craig Forrest.

上图是Java初创团队的全家福，Java创始人James Gosling大家应该都能认出来，Chris Warth就在他的左边，具体是哪个，就不得而知了，估计是戴眼镜那个。

# 类图



![img](./observer/80cc320356d24ff698c03bdc4baac81d~noop.image)



**类与接口**

java.util.Observer：接口，观察者角色

java.util.Observable:类，被观察者角色

**原著中的观察者模式**

![img](./observer/d614cfec1dfc46a2af88b34cc58e627f~noop.image)



上图，是GOF在其原著中对设计模式的结构定义，**Chris Warth**在设计和实现JDK的观察者模式时，基本上尊重了原著。Observer几乎一模一样。而原著中的Subject（主题），被命名为了Observable（被观察者），我喜欢这个名字，语义比较连贯，这也是我为什么在用REIS模型分析观察者设计模式时，把它里面的角色定义为观察者角色（Observer）和被观察者角色（Observable）的原因。

# java.util.Observer：接口，观察者角色

作为观察者角色，它里面只有一个update方法。

```
package java.util;

/**
 * A class can implement the <code>Observer</code> interface when it
 * wants to be informed of changes in observable objects.
 *
 * @author  Chris Warth
 * @see     java.util.Observable
 * @since   JDK1.0
 */
public interface Observer {
    /**
     * This method is called whenever the observed object is changed. An
     * application calls an <tt>Observable</tt> object's
     * <code>notifyObservers</code> method to have all the object's
     * observers notified of the change.
     *
     * @param   o     the observable object.
     * @param   arg   an argument passed to the <code>notifyObservers</code>
     *                 method.
     */
    void update(Observable o, Object arg);
}
```

# java.util.Observable:抽象类，被观察者角色

下面是Observable的类注释和源代码，大家可以的看一下，特别是出现“**synchronized**”关键字的地方，就会明白，我们在第二版实现的代码，虽然理论上正确，但是在真正的生产环境中，特别是在多线程环境中，是多么的不靠谱。

另外，这个类中，存储观察者的集合，为什么没有使用我们常见的List，而是用Vector，看过我上期的文章，就应该知道，在JDK1.0的时候，List集合还没有诞生。

对于多线程相关的内容，我在后面的《Java极客之并发编程》中，会给大家进行分享，敬请期待。

另外，还有一个要注意的地方，就是这个类的注释部分。

This class represents an observable object, or **"data" in the model-view** paradigm.

从这句话，可以看出观察者模式，与我们熟悉的MVC设计模式，有着千丝万缕的联系。Observable就相当于Model，而Observer，就相当于View。

![img](./observer/27798afcc2384e84b04798a8e8a2a8ab~noop.image)



```
package java.util;

public class Observable {
    private boolean changed = false;
    private Vector<Observer> obs;

    /** Construct an Observable with zero Observers. */

    public Observable() {
        obs = new Vector<>();
    }

    /**
     * Adds an observer to the set of observers for this object, provided
     * that it is not the same as some observer already in the set.
     * The order in which notifications will be delivered to multiple
     * observers is not specified. See the class comment.
     *
     * @param   o   an observer to be added.
     * @throws NullPointerException   if the parameter o is null.
     */
    public synchronized void addObserver(Observer o) {
        if (o == null)
            throw new NullPointerException();
        if (!obs.contains(o)) {
            obs.addElement(o);
        }
    }

    /**
     * Deletes an observer from the set of observers of this object.
     * Passing <CODE>null</CODE> to this method will have no effect.
     * @param   o   the observer to be deleted.
     */
    public synchronized void deleteObserver(Observer o) {
        obs.removeElement(o);
    }

    /**
     *
     * @see     java.util.Observable#clearChanged()
     * @see     java.util.Observable#hasChanged()
     * @see     java.util.Observer#update(java.util.Observable, java.lang.Object)
     */
    public void notifyObservers() {
        notifyObservers(null);
    }

    /**
     * If this object has changed, as indicated by the
     * <code>hasChanged</code> method, then notify all of its observers
     * and then call the <code>clearChanged</code> method to indicate
     * that this object has no longer changed.
     * <p>
     * Each observer has its <code>update</code> method called with two
     * arguments: this observable object and the <code>arg</code> argument.
     *
     * @param   arg   any object.
     * @see     java.util.Observable#clearChanged()
     * @see     java.util.Observable#hasChanged()
     * @see     java.util.Observer#update(java.util.Observable, java.lang.Object)
     */
    public void notifyObservers(Object arg) {
        /*
         * a temporary array buffer, used as a snapshot of the state of
         * current Observers.
         */
        Object[] arrLocal;

        synchronized (this) {
            /* We don't want the Observer doing callbacks into
             * arbitrary code while holding its own Monitor.
             * The code where we extract each Observable from
             * the Vector and store the state of the Observer
             * needs synchronization, but notifying observers
             * does not (should not).  The worst result of any
             * potential race-condition here is that:
             * 1) a newly-added Observer will miss a
             *   notification in progress
             * 2) a recently unregistered Observer will be
             *   wrongly notified when it doesn't care
             */
            if (!changed)
                return;
            arrLocal = obs.toArray();
            clearChanged();
        }

        for (int i = arrLocal.length-1; i>=0; i--)
            ((Observer)arrLocal[i]).update(this, arg);
    }

    /**
     * Clears the observer list so that this object no longer has any observers.
     */
    public synchronized void deleteObservers() {
        obs.removeAllElements();
    }

    /**
     * Marks this <tt>Observable</tt> object as having been changed; the
     * <tt>hasChanged</tt> method will now return <tt>true</tt>.
     */
    protected synchronized void setChanged() {
        changed = true;
    }

    /**
     * Indicates that this object has no longer changed, or that it has
     * already notified all of its observers of its most recent change,
     * so that the <tt>hasChanged</tt> method will now return <tt>false</tt>.
     * This method is called automatically by the
     * <code>notifyObservers</code> methods.
     *
     * @see     java.util.Observable#notifyObservers()
     * @see     java.util.Observable#notifyObservers(java.lang.Object)
     */
    protected synchronized void clearChanged() {
        changed = false;
    }

    /**
     * Tests if this object has changed.
     *
     * @return  <code>true</code> if and only if the <code>setChanged</code>
     *          method has been called more recently than the
     *          <code>clearChanged</code> method on this object;
     *          <code>false</code> otherwise.
     * @see     java.util.Observable#clearChanged()
     * @see     java.util.Observable#setChanged()
     */
    public synchronized boolean hasChanged() {
        return changed;
    }

    /**
     * Returns the number of observers of this <tt>Observable</tt> object.
     *
     * @return  the number of observers of this object.
     */
    public synchronized int countObservers() {
        return obs.size();
    }
}
```

我们看看如何基于JDK里面的观察者模式，重构第一版代码，注意，是第一版代码，不是第二版代码。

# 类图

![img](./observer/716efb794a0b4744937f6646197a3c6e~noop.image)



**类及接口**

OrderServiceV3Observable：被观察者角色，订单服务类

BookBusinessServiceV3：观察者角色，图书业务服务实现类

MobileBusinessServiceV3：观察者角色，手机业务服务实现类

# OrderServiceV3Observable：被观察者角色，订单服务类

这个类的代码，因为继承了Observable，所以比第二版的订单服务类精简了不少。

```
package com.geekarchitect.patterns.observer.demo03;

import com.geekarchitect.patterns.observer.demo01.OrderInfo;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Service;

import java.util.Observable;

/**
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
@Service
@Scope("prototype")
public class OrderServiceV3Observable extends Observable implements InitializingBean {
    private static final Logger LOG = LoggerFactory.getLogger(OrderServiceV3Observable.class);

    public void pay(OrderInfo orderInfo) {
        LOG.info("订单中台：订单支付成功");
        this.setChanged();//修改状态
        this.notifyObservers(orderInfo);//通知观察者
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        LOG.info("订单中台：初始化垂直业务部门");
        this.addObserver(new MobileBusinessServiceV3());
        this.addObserver(new BookBusinessServiceV3());
    }
}
```

# BookBusinessServiceV3：观察者角色，图书业务服务实现类

```
package com.geekarchitect.patterns.observer.demo03;

import com.geekarchitect.patterns.observer.demo01.OrderInfo;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Observable;
import java.util.Observer;

/**
 * 手机业务服务
 *
 * @author 极客架构师@吴念
 * @createTime 2022/11/17
 */
public class BookBusinessServiceV3 implements Observer {
    private static final Logger LOG = LoggerFactory.getLogger(BookBusinessServiceV3.class);

    @Override
    public void update(Observable o, Object arg) {
        OrderInfo orderInfo = (OrderInfo) arg;
        LOG.info("垂直业务部门：图书业务服务,订单信息{}", orderInfo);
    }
}
```



# 运行结果

![img](./observer/89037c7bf36c4a599e1d0df7fa4ecbfd~noop.image)



# 案例解析

这版代码，我们基于JDK的观察者模式模块来实现，所以省去了很多工作，对于Observable类，我们要注意两点。

1，因为java是单一继承制，所以一旦继承了Observable，我们的类，就不能再有自己的父类。可以考虑用组合的方式试试。

2，Observable类是有状态的，需要先调用setChanged()，再调用notifyObservers(orderInfo)。

下面，我们基于REIS模型，对观察者模式进行解析。

# REIS模型分析观察者模式

REIS模型是我总结的分析设计模式的一种方法论，主要包括场景（scene），角色（role），交互（interaction），效果(effect)四个要素。

# 角色（Role）

角色，一般为设计模式出现的类，或者对象。每种角色有自己的职责。

在观察者模式中，原著中定义了四种角色，如下图所述，分别是观察者接口（Observer）和观察者具体类（ConcreteObserver）和主题接口（Subject）和主题具体类（ConcreteSubject）。

![img](./observer/f8d76f56a8104aa4aa94f9abb7b0db22~noop.image)



以我的观点，在这个设计模式中，区分角色的接口和具体类，没有实际意义。所以，我认为，观察者模式，有四种角色，分别是观察者角色（Observer Role），被观察者角色（Obserable Role），消息角色（Message role），以及缓冲池（cache pool role）。

**观察者角色（Observer Role）**

观察者角色（在观察者模式的各种化身中，也可以称为订阅者角色（subscribe role），监听器角色（Listener role），模型角色（Model role），消费者角色（consumer role））。

它在依赖关系中属于依赖方，在一对多关系中，属于“多”方，它的职责就是，在被观察者角色发生变化时，根据业务需要，执行自己的业务逻辑。所以这个角色里面的方法比较简单，一般只需要定义自己的业务方法即可。

观察者模式的各种化身，我们后面有详细说明。



**被观察者角色（Obserable Role）**

被观察者角色，（在观察者模式的各种化身中，也可以称为发布者角色（Publish role）,事件源角色（event source role），模型角色（View role），生产者角色（producer role））。

它在依赖关系中属于被依赖方，在一对多关系中，属于“一”方，它的职责可以从两方面考虑。

一方面，就是有自身的业务逻辑，并造成一定的状态变化或者事件发生。

另一方面，也是更为重要的一方面，就是要把状态变化或者事件发生，通知出去，或者叫发布出去，让所有的观察者都知道这件事。

至于如何做，至少可以有两个选择。

1，被观察者自己逐个通知观察者，这样双方耦合度高。（我们上面的案例代码就是这种方式）

2，把消息发送给，或者委托给第三方，也就是消息缓冲池（消息系统），然后由消息缓存池，负责将消息发布出去，这样观察者与被观察者，就彻底解耦合了。

如果采用的是第一种方案，被观察者角色里面，就需要定义增加，删除和通知观察者的相应方法。

如果采用第二种方案，就意味着，需要使用专业的消息系统或者叫消息引擎了，如ActiveMQ，Kafka***，***RocketMQ等等。

**消息角色（Message role）**

消息角色，顾名思义，就是封装消息的对象，（在观察者模式的各种化身中，它也可以称为事件（Event），实体类（Entity），产品（product）等等）。它的职责简单，就不多说了。



**缓冲池角色（cache pool role）**

缓冲池角色，是用来解耦合观察者和被观察者的角色，这个角色简单一些的，可以是集合类，稍微复杂一些的，就是消息系统或者叫消息引擎了。

要注意缓冲的内容，可以是消息，也可以是观察者角色，在简单的观察者模式中，通常是缓冲观察者，而在消息引擎中，通常是缓冲消息或者事件的。

要实现一个高效的，确保消息不漏发，不重发，而且要支持各种类型的消息，不是一件容易的事情。这个我们在后面的专题分享中，会给大家介绍。

# 交互（interaction）

交互，是指设计模式中，各种角色是如何交互的，一般用UML中的序列图，活动图来表示。简单的说就是角色之间是如何配合，完成设计模式的使命的。

对于观察者模式，角色之间的交互，可以分为两个阶段：

**1，初始化阶段**

1，可以由被观察者将依赖自己的所有观察者，加入到缓冲池集合中。

2，或者观察者角色，在缓冲池角色，注册相应的消息和事件（消息系统中的流程）

**2，运行阶段**

1，被观察者角色，在事件发生后，封装消息实体。

2，如果存在消息缓冲池，被观察者就将消息发送到消息缓冲池中，反之，则直接逐个发送给观察者角色。

3，观察者角色，在接到通知后，根据消息信息，判断是否需要处理消息，根据自己的业务逻辑，进行业务处理。

# 场景（Scene）

场景，也就是我们在什么情况下，遇到了什么问题，需要使用某个设计模式。

对于观察者模式，由于它有很多化身，如MVC模式，发布订阅模式（Publish-Subscribe），事件监听机制，生产者消费者模式，所以使用的场景非常多。大家在工作中接触的比较多的，应该有web项目的MVC架构，多线程里面的线程池，java Swing里面的事件监听，还有就是消息系统。

# 效果(effect)

效果，使用该设计模式之后，达到了什么效果，有何意义，当然，也可以说说它的缺点，或者风险。

从我们前面的案例可以看出，解释器模式达到了以下效果。

对于观察者模式，我认为它的优点，可以完全覆盖它的缺点，所以，我只说它的优点。

它的最大优点，就是通过消息缓冲池，实现了观察者和被观察者的完全隔离，这也是它能在分布式与微服务架构的今天，能成为中流砥柱的原因。



总结一下，基于REIS分析模型，观察者模式由四个角色组成，分别是观察者角色（Observer Role），被观察者角色（Obserable Role），消息角色（Message role），以及缓冲池角色（cache pool role），它的宗旨是，通过缓冲池角色，实现观察者与被观察者的解耦合。



# 观察者模式的化身

在GOF原著中，观察者模式，除了正式名称Observer之外，还有两个别名，就是Dependents和Publish-Subscribe。

Dependents除了名字，没有其他辅助文献资料，对其进行详细的阐述，来龙去脉无从考究（有知道的可以评论区留言，或者私信我，重重有赏哦）。

# 发布订阅模式（Publish-Subscribe）

而Publish-Subscribe，就是大名鼎鼎的发布订阅模式，常常现身于各种消息引擎系统中，如老牌的ActiveMQ，炽手可热的Kafka***，***阿里系的RocketMQ等等。关于发布订阅模式，我会在分布式异步消息框架的相关分享中详细介绍。

而同时和发布订阅模式，出现在消息系统的，还有另外一个设计模式，就是生产者消费者模式。

# 生产者消费者模式

生产者消费者模式，通常作为并发编程的一种设计模式，它的起源，是有据可查的，它源自艾兹格·迪科斯彻的一篇论文。

> “We consider two processes, which are called the 'producer' and the 'consumer' respectively. The producer is a cyclic process that produces a certain portion of information, that has to be processed by the consumer. The consumer is also a cyclic process that needs to process the next portion of information, as has been produced by the producer We assume the two processes to be connected for this purpose via a buffer with unbounded capacity.”

![img](./observer/12495282f0194d3a8ae843928434008b~noop.image)



艾兹格·迪科斯彻，荷兰的计算机科学家，72年获得图灵奖，被誉为“结构程序设计之父”，他的成就很多，说几个大家可能比较熟悉的，他提出了**“goto有害论”**，他解决了著名的**“哲学家聚餐”**问题，也就是多线程的问题。

关于生产者消费者模式，我会在即将推出的《java极客之并发编程》中的线程池部分，进行详细讲解。

# MVC模式

什么是MVC（Model–view–controller），我就不多说了，为什么说观察者模式隐身于MVC模式之中呢。

在GOF的原著中，列举的观察者模式的案例，就是一个典型的MVC模式的案例，同一个数据源，显示在不同的视图组件中（表格，柱状图，饼图）。

三个不同的视图View（Observer角色），显示同一个模型Model的数据（Observable角色）。

![img](./observer/2ea7c8082f8940eba3dca4dabd71819e~noop.image)



作者在原文中对MVC与观察者模式的关系，阐述如下所示。

> The first and perhaps best-known example of the Observer pattern appears in Smalltalk Model/View/Controller (MVC), the user interface framework in the Smalltalk environment [KP88]. MVC's Model class plays the role of Subject, while View is the base class for observers.

MVC设计模式中的视图（View）对应观察者模式中的Observer，模型（Model）对应主题（Subject）（也就是我们所说的被观察者Observable）

# 事件监听机制（Event-Listener）

事件监听机制，大部分java程序员接触的比较少，熟悉java swing的朋友，应该会有所了解。那它和观察者模式，有什么关系呢？

观察者，英语是“observer”，可以翻译为观察者，目击者；观察家。

但是，不论是观察者，目击者好像都是用眼睛的，用耳朵就不能观察了吗，对于我这种被国产抗日神剧和谍战剧洗礼的老码农，谍战教父柳云龙的忠实粉丝，谍战剧中我党的卧底人员，潜入中统或者军统内部，用窃听器窃听敌人秘密会议的镜头还历历在目，如果用耳朵听，也是观察，那么listener和Observer，是不是就殊途同归了呢。

![img](./observer/94e4c388406740e189e762b7fc4ed28d~noop.image)



好像是胡说八道，牵强附会啊。

这个还真不是我忽悠大家，大家在百度搜一下“事件监听机制 观察者模式”,在Bing国际版搜索“event listener observer”，也可以看到不少相关文章，不过，关于这两个设计模式，特别权威的文章，我目前还没有发现，有知道的，可以私信我，或者评论区晒一晒哦。

我的观点，观察者模式，与MVC模式，事件监听模式，发布订阅模式，生产者消费者模式，虽然名字各异，但是本质相同，不同的业务场景，各有侧重，殊途同归，貌离神合。

大家怎么看，可以评论区留言。

# 总结如下

![img](./observer/36476eb644e0458e8a3a85f47a41e9d2~noop.image)



# 通用代码和类图

对于解释器这种级别的设计模式，通用代码没有任何实际价值，来参考价值都没有，所以，我就提供了。大家如果感兴趣，可以到网上搜搜，借鉴一下。

![img](./observer/6d69e517374046039fcf5246ecf48623~noop.image)



**接口及类**

IObservable：被观察者接口

AbstractObservable：被观察者抽象类

ConcreteObservableA：被观察者实现类

IObserver：观察者接口

ConcreteObserverA：观察者实现类A

ConcreteObserverB：观察者实现类B

Message：消息

相关代码，大家到我的github上下载即可。



# 下期预告

观察者模式，我们就分享到这里，我们今天重点讲解了观察者模式的真身，对于它的其他各种化身，我在后面的相关专题中，会一一详细分享，敬请期待。下期，我们将分享最后一个行为型设计模式——中介者模式（Mediator Pattern）。

极客架构师，专注架构师成长。

关注我，我将持续分享更多架构师的相关文章和视频，我们下期见。