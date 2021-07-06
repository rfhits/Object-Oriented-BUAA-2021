# Unit4

不知道你吐了没有，反正我是吐了🤮。

先是官方包里要导入额外的包，然后我的IDEA`find jar on web`没反应，然后在我国的互联网垃圾堆里翻了许久，P都没找到。

接着在伟大的Stackoverflow上，终于找到了解决方法。

**泪，射了出来。**

这悲惨的遭遇，不禁想起做Unit3时，我竟然花了30分钟研究怎么导包。

UmlElement里，什么是Operation，什么是Parameter？我一脸蒙蔽。

为什么叫Operation不叫Method，，你，，你可以强迫我用Operation这个单词，但是课程组你得给我个理由知道吧。

到了下午，慢慢看，不得不说，这OO有OS内味了，感觉自己在解谜啊。

## 写在前面

如果对UML一窍不通，那这个单元可以直接结课了。

[UML快速入门](https://blog.csdn.net/endlessseaofcrow/article/details/85855461)。

https://www.cnblogs.com/wolf-sun/p/UML-Sequence-diagram.html

特别注意官方包models/elements下的那些问价，就是UML类图的“组件”。

想要弄懂那些英文单词，可以看[这个](https://javapapers.com/oops/association-aggregation-composition-abstraction-generalization-realization-dependency/)。

本文章参考此[博客](https://www.cnblogs.com/puzzledAtticus/p/11076553.html)。

## 要干什么

难点在于，我们不知道这些Element具体是干什么的，以及各个ELement里面的成员变量是做什么的，这些类具体有哪些方法。

### hw1

输入特定格式的UmlElement，将其存储到对应的容器，并构建关系。

实现查询时的接口。

特点：`add by id, query by name`

id保证不重复（言外之意，name保证存在重复）。

还有一个我个人认为非常恶心的地方，

在加入，或者说初始化这些元素的时候，就应该做Exceptions的check，不合法的东西就不应该被添加进来，而不是放到query的时候再来做。

指导书有些地方写的不清楚是肯定的，比如说异常的抛出，不过如果按照我们的思路，函数式编程地来写指导书，那就把思路都告诉我们了，难度就下去。

### hw2

> 保证输入均从合法UML图中导出，但可能会存在部分上层元素parentid不存在的情况。
>
> 起点和终点确实最多只有一个

可能输入多个State Machine Diagram

## Class Diagram

这玩意最好自底向上看。

### UmlParameter

参数

```java
private UmlParameter(AbstractElementData elementData, Direction direction, NameableType type) {
    super(elementData);
    this.direction = direction;
    this.type = type;
}
```

尤其要注意type

### UmlOperation

```java
private UmlOperation(AbstractElementData elementData, Visibility visibility) {
    super(elementData);
    this.visibility = visibility;
}
```

需要我们封装，能够区分这个Operation有无返回值，有无接收参数，这些参数是什么。

最好封装下接收的参数、返回的参数，因为要统计操作的参数类型。

### UmlAttributes

```java
private UmlAttribute(AbstractElementData elementData, Visibility visibility, NameableType type) {
    super(elementData);
    this.visibility = visibility;
    this.type = type;
}
```

parentId表示是哪个类的attrib

### UmlClass

```java
private UmlClass(AbstractElementData elementData, Visibility visibility) {
    super(elementData);
    this.visibility = visibility;
}
```

需要封装。

把attribute和operation放好。

### UmlAssociationEnd

```java
private UmlAssociationEnd(AbstractElementData elementData, Visibility visibility, String multiplicity, String reference, Aggregation aggregation) {
    super(elementData);
    this.visibility = visibility;
    this.multiplicity = multiplicity;
    this.reference = reference;
    this.aggregation = aggregation;
}
```

只是一种链接到了某个UMLClass的结构。

### UmlAssociation

只是表示某种链接的关系。如`企鹅`和`气候`。

链接到两个UMLAssociationEnd的Id。

> * UMLAssociation
>   + end1：对应其中一个UMLAssociationEnd的Id
>   + end2：对应另外一个UMLAssociationEnd的Id

然后我们要通过Id获取到这两个UMLAssociationEnd，再通过End的getRef得到ClassId。

### UmlInterface

注意接口与接口之间的继承关系。

可以为接口维护自己的父接口，这样查询一个类对应所有父接口时可以快。

### UmlInterfaceRealization

```java
private UmlInterfaceRealization(AbstractElementData elementData, String source, String target) {
    super(elementData);
    this.source = source;
    this.target = target;
}
```

* **_parent**：同成员变量source
* source：对应实现该接口的类的ID
* target：对应该接口的ID

### UmlGeneralization

表示继承，如`Person`和`Student`。

* UMLGeneralization
  * **_parent**：同source，对应子类ID
  * source：对应子类ID
  * target：对应父类ID

## State Machine Diagram

* 状态图相关
  * 确保每个State Machine中有且仅有一个Region；
  * 确保每个State Machine中最多只有一个Initial State，最多只有一个Final State；
  * 确保每个State Machine中Initial State和Final State的name均为null，查询指令中给出的状态也不会为Initial State或Final State；
  * 确保每个State Machine中，从某个状态到另一个状态的直接迁移均具有不同的Event或Guard，即从某个状态到另一个状态的直接迁移若有多个，则这些迁移一定互不相同；
  * 确保每个State Machine中，Initial State没有状态迁入，Final State没有状态迁出；
  * 确保每个State Machine中不包含复合状态。
* *

### UmlStateMachine

就是整个状态机。内含状态（State），Transition

### UmlRegion

链接到一个状态机


### UmlState

* `_parent`链接到一个`UmlRegion`

### UmlFinalState

### UmlEvent

一个事件

* `_parent`链接到一个`UmlTransition`
*

### UmlTransition

* `_parent`链接到一个`UmlRegion`
*

### UmlRegion

* `_parent` 链接到一个`UmlStateMachine`
*

## Sequence Diagram

顺序图相关确保每个顺序图中，Lifeline和其Represent均一一对应。

### UmlInteraction

抽象地表示了**整个顺序图**

### UmlLifeLine

就是顺序图的参与对象

### UmlMessage

* `parent`链接到一个UmlInteraction
* `targetId`链接到接收消息的对象
* `sourceId`哪个lifeline发的
* `messageSort`，表示消息的类型，同步的，还是reply

## 封装

### MyClass

封装类

### MyOperation

封装方法，内含attribute

### MyInteraction

封装整个顺序图（Sequence Diagram）

内含LifeLine，Message。

### MyStateMachine

封装整个状态图，

内含State，Transition

## 指令

### getType()

UmlAttribute和UmlParameter都有一个成员变量

```
private final NameableType type;
````

getType()后判断instance即可

```java
String type = null;
if (umlAttrib.getType() instanceof NamedType) {
    type = ((NamedType) umlAttrib.getType()).getName();
    if (!namedTypeSet.contains(type)) { // not contains
        throw new AttributeWrongTypeException(className, attributeName);
    }
else { 
  
}
```

### 类属性类型

坑点：

1. 类型可以是接口

### 类操作的参数类型

**1、注意异常的抛出顺序**

类的属性类型优先抛出重复异常，再抛出类型错误异常。而类的操作的参数类型是优先抛出类型错误异常，再抛出重复异常。

**2、获取基本类型和引用类型的name方式不一样**

基本类型的name是通过JsonString的substring获得的。而引用类型的name是要通过referenceId找到对应的类或者接口，再getname获得的，注意，引用类型可能是**类**也可能是**接口**。

**3、关联和继承和实现**

* association只需要保存类和类之间的。
* generalization需要保存类和类之间的以及接口和接口之间的，同时要注意类和类之间只可能有一次继承，一个接口却可以继承多个接口。
* 只可能是类实现接口。
* target是被继承/实现的对象，source是子类/实现接口的类。

**4、注意不重不漏**

建议用一个id的list或者<id,name>的hashmap保证不重不漏。

**5、参数和返回值的基本类型情况不同**

返回值的基本类型多了一种void，判断的时候需要注意一下。

**6、注意每个指令单独的注意事项**

如需不需要考虑继承自父类的属性，出现同名属性是抛出异常还是分别计数（每个指令的要求不尽相同）。

注意返回的是`List<OperationParamInformation>`

* parameterTypes中需要包含**除了返回值**的所有参数类型。
* 需要判断该操作是否有返回值，若有，则将returnType设置为相应类型；若没有返回值，则将returnType设置为null，**注意，这里的null为关键字null，而非字符串“null”**。
* 对于**返回值类型正确性**的判断与之前不同，正确类型多了个**void**。
* 注意存在错误类型和重复操作的异常优先级，根据要求是存在错误类型异常优先级**高于**重复操作异常。

in参数类型允许是void

OperationParamInformation的`equals`方法可以直接用来比较

MyOperation内部无法生成Info，必须放出权限让Inter生成。即包含get方法。

1. 确认Name对应的Class只有一个
2. 取出该Class
3. Class提供给出OpName返回一个OperationSet的函数，取出所有为此Name的Op
4. OpSet现在在Inter中，遍历Set，为每个Op生成ParaInfo，可抛出异常
5. 将Info收集，在Info进入list前，做contains判断
   1. 若已经含，则抛异常
   2. 若不含，则放入list
6. 将infolist返回

### 触发条件

先遍历transition，确认存在性

遍历event，根据parentId找到transition，将eventName收集

### incoming Message

接收到的消息的个数。

### 发送的对应类型消息的个数


## bug

### hw1


### hw2

#### 1

```java
for {
    for (element ) {
        sort
        setUp()
    }
}
```

setUp应该在for之后

#### 2

统计数量，容器用错

```
if (seqName2MySeq.containsKey(interName)) {
            mySeqName2Num.put(interName, mySeqName2Num.get(interName) + 1);
        } else {
            mySeqName2Num.put(interName, 1);
        }
```
