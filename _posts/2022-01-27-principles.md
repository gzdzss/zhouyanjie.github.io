---
layout:     post
title:      "浅谈软件设计原则(持续更新中)"
subtitle:   "所谓的设计原则就是在大量的工程实践的基础上以及科学研究的基础上总结出来的一些经验和理念。"
date:       2022-02-10 18:00:00
author:     "Andrew"
catalog: false
header-style: text
tags:
  - 设计原则 
---


> 首先，为什么要有软件设计原则？软件设计原则的目的是为了让我们编写出更好的代码，那什么是“更好的代码”？“更好的代码”就是使代码更简洁、更易读、更具有可维护性以及更具有可扩展性。那么我们写代码或者设计代码结构的时候不遵循软件设计原则可以吗？答案是可以的。因为软件设计原则不像是Java语法一样的硬性要求，不这么做编译就不通过，你的程序就运行不了，相反，不遵循这七大设计原则你的代码照样能够运行。那么所谓的设计原则就是在大量的工程实践的基础上以及科学研究的基础上总结出来的一些经验和理念，我们在设计以及编写代码的过程中要尽量地借鉴前人的一些好的经验来使我们自己少走弯路，这也是软件设计原则的意义所在 [[1]](https://blog.csdn.net/u013655410/article/details/104410375)。

### 七大设计原则
1. [开闭原则](#1开闭原则 "开闭原则")
2. [依赖倒置原则](#2依赖倒置原则 "依赖倒置原则")
3. [单一职责原则](#3单一职责原则 "单一职责原则")
4. [接口隔离原则](#4接口隔离原则 "接口隔离原则")
5. [迪米特原则](#5迪米特原则 "迪米特原则")
6. [里氏替换原则](#6里氏替换原则 "里氏替换原则")
7. [合成复用原则](#7合成复用原则 "合成复用原则")


### 1.开闭原则
#### 1.1定义
> 开闭原则（Open Closed Principle，OCP）由勃兰特·梅耶（Bertrand Meyer）提出，他在 1988 年的著作《面向对象软件构造》（Object Oriented Software Construction）中提出：软件实体应当对扩展开放，对修改关闭（Software entities should be open for extension，but closed for modification），这就是开闭原则的经典定义。
>> ***即当应用的需求改变时，在不修改软件实体的源代码或者二进制代码的前提下，可以扩展模块的功能，使其满足新的需求。***

#### 1.2示例
> 假设有一个水果店，该水果店现在出售：“苹果、香蕉”[<sub>[2]</sub>](https://baijiahao.baidu.com/s?id=1670636817035694210&wfr=spider&for=pc)。
>> 水果基类
```java
/**
 * 基类
 */
public abstract class Fruit {
    protected int type;
}
/**
 * 苹果
 */
public class Apple extends Fruit {
    public Apple() {
        this.type = 1;
    }
}
/**
 * 香蕉
 */
public class Banana extends Fruit {
    public Banana() {
        this.type = 2;
    }
}
```
>> 水果店
```java
public class FruitShop {
    public void sellFruit(Fruit fruit) {
        if (fruit.type == 1) {
            sellApple(fruit);
        } else if (fruit.type == 2) {
            sellBanana(fruit);
        }
    }
    private void sellApple(Fruit fruit) {
        System.out.println("卖出了一斤苹果！");
    }
    private void sellBanana(Fruit fruit) {
        System.out.println("卖出了一斤香蕉！");
    }
}
```
>> 执行测试类
```java
    public static void main(String[] args) {
        FruitShop shop =  new FruitShop();
        shop.sellFruit(new Apple());
        shop.sellFruit(new Banana());
    }
//执行结果:
//卖出了一斤苹果！
//卖出了一斤香蕉！
```

> 现在水果店扩张，添加了一种新的水果（西瓜）。根据以上示例代码我们需要作出如下新增。
>> 1、新增西瓜类
```java
/**
 * 西瓜
 */
public class Watermelon extends Fruit {
    public Watermelon() {
        this.type = 3;
    }
}
```
>> 2.FruitShop类中添加“卖西瓜”的方法
```java
public class FruitShop {
    ...
    private void sellWatermelon(Fruit fruit) {
        System.out.println("卖出了一斤西瓜！");
    }
}
```
>> 3.修改FruitShop类中的sellFruit方法
```java
 public void sellFruit(Fruit fruit) {
        ...
        } else if (fruit.type == 3) {
            sellWatermelon(fruit);
        }
    } 
```
>> 4.执行测试类
```java
public static void main(String[] args) {
        FruitShop shop =  new FruitShop();
        shop.sellFruit(new Apple());
        shop.sellFruit(new Banana());
        shop.sellFruit(new Watermelon());
    }
//执行结果:
//卖出了一斤苹果！
//卖出了一斤香蕉！
//卖出了一斤西瓜！
```

通过以上三步就实现了增加一种水果的需求，但是大家有没有发现，这种方式虽然容易理解，可是当功能发生变动时，代码的修改量会特别大。并且这种方式也**不符合“开闭原则”**，大家能看出来吗？

> 我们可以将以上代码进行如下优化：
>> 1.水果基类添加抽象售卖方法
```java
public abstract class Fruit {
    ...
    public abstract void sell();
}
```
>> 2.水果店售卖改造
```java
public class FruitShop {
    public void sellFruit(Fruit fruit) {
        fruit.sell();
    }
}
```
>> 3.对应的水果改造
```java
public class Apple extends Fruit {
    @Override
    public void sell() {
        System.out.println("卖出类一斤苹果!");
    }
}
public class Banana extends Fruit {
    @Override
    public void sell() {
        System.out.println("卖出类一斤香蕉!");
    }
}
public class Watermelon extends Fruit {
    @Override
    public void sell() {
        System.out.println("卖出类一斤西瓜!");
    }
}
```
>> 4.执行测试方法
```java
public static void main(String[] args) {
        FruitShop shop =  new FruitShop();
        shop.sellFruit(new Apple());
        shop.sellFruit(new Banana());
        shop.sellFruit(new Watermelon());
    }
//执行结果:
//卖出了一斤苹果！
//卖出了一斤香蕉！
//卖出了一斤西瓜！
```

demo地址:[https://github.com/gzdzss/blog-demo/tree/main/software-design-principles/src/main/java/ocp](https://github.com/gzdzss/blog-demo/tree/main/software-design-principles/src/main/java/ocp)

#### 1.3结论
这样优化后的代码就**遵守了“开闭原则”**。提供方可以对系统进行扩展（对扩展开放），当系统扩展了新的功能后不会影响到使用方，使用方不需要进行修改（对修改关闭）。

- 尽量通过扩展软件的模块、类、方法，来实现功能的变化，而不是通过修改已有的代码来完成。
- 这样做就可以大大降低因为修改代码而给程序带来的出错率。



### 2.依赖倒置原则
#### 2.1定义
> 依赖倒置原则（Dependence Inversion Principle，DIP）是 Object Mentor 公司总裁罗伯特·马丁（Robert C.Martin）于 1996 年在 C++ Report 上发表的文章。高层模块不应该依赖低层模块，两者都应该依赖其抽象；抽象不应该依赖细节，细节应该依赖抽象（High level modules shouldnot depend upon low level modules.Both should depend upon abstractions.Abstractions should not depend upon details. Details should depend upon abstractions）。
>> ***即要面向接口编程，不要面向实现编程。***
#### 2.2示例
> 假设我们现在有两辆车，一辆奔驰，一辆宝马，然后都需要让司机开车[<sub>[3]</sub>](https://www.jianshu.com/p/b68e2cc9b4f5)。
>> 
```java
/**
 * 宝马
 */
public class BMWCar {
    public void run() {
        System.out.println("宝马开动了！");
    }
}
/**
 * 奔驰
 */
public class BenzCar {
    public void run() {
        System.out.println("奔驰开动了！");
    }
}
/**
 * 司机
 */
public class Driver {
    //开宝马
    public void driveBMWCar(BMWCar car) {
        car.run();
    }
    //开奔驰
    public void driveBenzCar(BenzCar car) {
        car.run();
    }
}
```
>> 执行测试类
```java
    public static void main(String[] args) {
        Driver driver = new Driver();
        driver.driveBMWCar(new BMWCar());
        driver.driveBenzCar(new BenzCar());
    }
//宝马开动了！
//奔驰开动了！
```

上面的代码好像没有什么问题。 那么如果现在又要新增特斯拉、奥迪、罗斯莱斯等车呢？难道要为每一辆新增的车去修改司机类？这显然是荒唐的。依赖于具体类，会导致类之间的耦合性太强，这就是在代码中依赖具体类的问题。

> 我们可以将以上代码进行如下优化：
>> 1.抽象车类
```java
public interface Car {
    void run();
}
```
>> 2.司机改造（注意：此时司机依赖的为抽象的车类，而不是具体的车类）
```java
public class Driver {
    //开车
    public void driveCar(Car car) {
        car.run();
    }
}
```
>> 3.车类改造(只要具体的车类实现自抽象车类，那么无论是什么车，司机都可以开)
```java
/**
 * 宝马
 */
public class BMWCar implements Car {
    public void run() {
        System.out.println("宝马开动了！");
    }
}
/**
 * 奔驰
 */
public class BenzCar implements Car {
    public void run() {
        System.out.println("奔驰开动了！");
    }
}
/**
 * 特斯拉
 */
public class TslaCar implements Car {
    public void run() {
        System.out.println("特斯拉开动了！");
    }
}
```
>> 4.执行测试方法
```java
    public static void main(String[] args) {
        Driver driver = new Driver();
        driver.driveCar(new BenzCar());
        driver.driveCar(new BenzCar());
        driver.driveCar(new TslaCar());
    }
//奔驰开动了！
//奔驰开动了！
//特斯拉开动了！
```

demo地址:[https://github.com/gzdzss/blog-demo/tree/main/software-design-principles/src/main/java/dip](https://github.com/gzdzss/blog-demo/tree/main/software-design-principles/src/main/java/dip)

#### 2.3结论
通过上面的例子，相信大家已经领略到在代码中使用依赖倒置原则的重要性了。总结一下依赖倒置原则的优点：

- 减少类之间的耦合
- 降低并行开发引起的风险
- 提高代码的可读性和可维护性

### 3.单一职责原则
#### 3.1定义
> 单一职责原则（Single Responsibility Principle，SRP）又称单一功能原则，由罗伯特·C.马丁（Robert C. Martin）于《敏捷软件开发：原则、模式和实践》一书中提出的。这里的职责是指类变化的原因，单一职责原则规定一个类应该有且仅有一个引起它变化的原因，否则类应该被拆分（There should never be more than one reason for a class to change）。
>>  ***即一个类/接口/方法只负责一项职责***

#### 3.2示例
> 假设我们有一个类，用来识别动物的主要"主要移动方式"
>> 动物
```java
/**
* 动物
*/
public class Animal {
   public void mainMoveMode(String animalName) {
       System.out.println(animalName + "用翅膀飞");
   }
}
```
>> 测试方法
```java
public static void main(String[] args) {
       Animal animal = new Animal();
       animal.mainMoveMode("小鸟");
   }
//小鸟用翅膀飞
```

 目前看好像没有什么问题，这时候我们新的动物"小狗"需要来识别"主要移动方式"， 如果我们套用这个类则输出"小狗用翅膀飞"， 这样明显不合理
>>于是我们需要进行如下改造改造:
```java
public class Animal {
    public void mainMoveMode(String animalName) {
        if ("小狗".equals(animalName)) {
            System.out.println(animalName + "用脚走路");
        } else {
            System.out.println(animalName + "用翅膀飞");
        }
    }
}
```
>> 测试方法
```java
public static void main(String[] args) {
    Animal animal = new Animal();
    animal.mainMoveMode("小鸟");
    animal.mainMoveMode("小狗");
}
//小鸟用翅膀飞
//小狗用脚走路
```

这样是实现功能，但是随着动物的变多，代码需要一直改动，这就违背了即一个类/接口/方法只负责一项职责的原则，于是我们可以进行以下改造
>>我们将动物划分为 "飞禽" 与 走兽 
```java
/**
  * 飞禽
  */
 public class Birds  extends  Animal {
     @Override
     public void mainMoveMode(String animalName) {
         System.out.println(animalName + "用翅膀飞");
     }
 }
 /**
  * 走兽
  */
 public class Beasts extends Animal {
     @Override
     public void mainMoveMode(String animalName) {
         System.out.println(animalName + "用脚走路");
     }
 }
```
>> 测试方法
```java
public static void main(String[] args) {
    Birds birds = new Birds();
    birds.mainMoveMode("小鸟");
    birds.mainMoveMode("小蜜蜂");
    Beasts beasts = new Beasts();
    beasts.mainMoveMode("小狗");
    beasts.mainMoveMode("小猫");
}
//小鸟用翅膀飞
//小蜜蜂用翅膀飞
//小狗用脚走路
//小猫用脚走路
```

demo地址:[https://github.com/gzdzss/blog-demo/tree/main/software-design-principles/src/main/java/srp](https://github.com/gzdzss/blog-demo/tree/main/software-design-principles/src/main/java/dip)

#### 3.3结论
- 对于不同的职责需要进行解耦。后期需求变更维护互不影响。
- 可以降低类的复杂度，提高类的可读性 ，提高系统的可维护性,降低变更引起的风险 。
- 总体来说即一个类/接口/方法只负责一项职责。


### 4.接口隔离原则
#### 4.1定义
> todo
#### 4.2示例
#### 4.3结论

### 5.迪米特原则
#### 5.1定义
> todo
#### 5.2示例
#### 6.3结论

### 6.里氏替换原则
#### 6.1定义
> todo
#### 6.2示例
#### 6.3结论

### 7.合成复用原则
#### 7.1定义
> todo
#### 7.2示例
#### 7.3结论




### 参考文献
- [[1]浅谈软件设计的七大原则·Daemon Zhang](https://blog.csdn.net/u013655410/article/details/104410375)
- [[2]作为程序员不可不会的设计模式七大原则之——“开闭原则”·编程小菜鸟](https://baijiahao.baidu.com/s?id=1670636817035694210&wfr=spider&for=pc)
- [[3]六大设计原则之三：依赖倒置原则·匆执羊](https://www.jianshu.com/p/b68e2cc9b4f5)