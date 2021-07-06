# Unit4

不知道你吐了没有，反正我是吐了🤮。

先是官方包里要导入额外的包，然后我的IDEA`find jar on web`没反应，然后在我国的互联网垃圾堆里翻了许久，P都没找到。

接着在伟大的Stackoverflow上，终于找到了解决方法。

**泪，射了出来。**

这悲惨的遭遇，不禁想起做Unit3时，我竟然花了30分钟研究怎么导包。

本文的目的在于记录与帮助，内容参考了此[博客](https://www.cnblogs.com/puzzledAtticus/p/11076553.html)。

打包的方法见[此博客](https://www.cnblogs.com/binbingg/p/13601702.html)。

## 写在前面

如果对UML一窍不通，那这个单元可以直接结课了。

首先，我们要知道`models/elements`下，那些.java文件是什么，哪些有用，哪些没用。

<img src="img/Unit4-elements.jpg" style="zoom:50%;" />

其次，我们要知道，这些`element`中，它的成员变量的作用是什么。

最后，我们要对输入的`elements[]`进行组织，使之满足课程的要求。

[UML类图快速入门](https://blog.csdn.net/endlessseaofcrow/article/details/85855461)。

[官方推荐的UML入门](https://www.cnblogs.com/wolf-sun/p/UML-Sequence-diagram.html)

`models/elements`下那些英文单词是什么意思，可以看[这篇文章](https://javapapers.com/oops/association-aggregation-composition-abstraction-generalization-realization-dependency/)。

## 要干什么

首先要知道，`models/elements`下的很多类，都继承了`UmlElement`。

我们要做的是，

1. 输入`UmlElement[]`，为它们建立起联系。具体地说，要将它们封装成相应的类，或是存储到对应的容器，组织起来；
2. 实现查询的接口。根据上一步自己的封装和各种容器，给出查询的答案。

### hw1

要求是，输入类图的elements，实现查询的接口。

#### 架构

千万不要把类都写到一起，否则这个Unit的代码行数绝对爆炸，代码风格最好就50分。

我推荐大家参考一位学长的代码，架构看起来非常牛逼，反正我挺后悔把代码写成一坨的。

学长代码见此：[BUAA_Object_Oriented_2019-sinoyou](https://github.com/sinoyou/BUAA_Object_Oriented_2019/tree/project-4-branch-2)

不要抄袭。

#### 难点

我们的作业有个特点——`add by id, query by name`

id保证不重复（言外之意，name保证存在重复）。

还有一个我认为非常恶心的地方，

在加入这些元素的时候，就应该做异常的检查。尤其是属性的类型，不合法的东西就不应该被添加进来，而不是放到query的时候再来做。

指导书有些地方写的不清楚是肯定的，比如说异常的抛出，不过如果按照我们的思路，函数式编程地来写指导书，那就把思路都告诉我们了，难度就下去。

### hw2

> 保证输入均从合法UML图中导出，但可能会存在部分上层元素parentid不存在的情况。
>
> 起点和终点确实最多只有一个

可能输入多个State Machine Diagram

### hw3

collaboration这个element没有输进来，但是会有几个element索引到它。

## Class Diagram

### UmlParameter

表示类图中，方法（Method）的参数。

```java
private UmlParameter(AbstractElementData elementData, Direction direction, NameableType type) {
    super(elementData);
    this.direction = direction;
    this.type = type;
}
```

* `direction`表示这个参数是输入还是返回
* `type`表示返回的类型

### UmlOperation

```java
private UmlOperation(AbstractElementData elementData, Visibility visibility) {
    super(elementData);
    this.visibility = visibility;
}
```

需要我们封装，能够区分这个`Operation`有无返回值，有无接收参数，这些参数是什么。

最好封装下接收的参数、返回的参数，因为要统计操作的参数类型。

### UmlAttribute

```java
private UmlAttribute(AbstractElementData elementData, Visibility visibility, NameableType type) {
    super(elementData);
    this.visibility = visibility;
    this.type = type;
}
```

* `parentId`表示是哪个类的`attribute`

### UmlClass

```java
private UmlClass(AbstractElementData elementData, Visibility visibility) {
    super(elementData);
    this.visibility = visibility;
}
```

需要封装，我的方法是，将其封装成`MyClass`，表示一张类图。

利用各种容器，存储类图中的attribute和operation。

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

* `reference`链接一个UmlClass。

### UmlAssociation

只是表示某种链接的关系。如`企鹅`和`气候`。

链接到两个UMLAssociationEnd的Id。

> * UMLAssociation
>   + end1：对应其中一个UMLAssociationEnd的Id
>   + end2：对应另外一个UMLAssociationEnd的Id

我们要通过Id获取到这两个UMLAssociationEnd，再通过End的getRef得到ClassId。

### UmlInterface

注意接口与接口之间的继承关系。

可以为接口维护自己的父接口，这样查询一个类对应所有父接口时可以快一点。

### UmlInterfaceRealization

```java
private UmlInterfaceRealization(AbstractElementData elementData, String source, String target) {
    super(elementData);
    this.source = source;
    this.target = target;
}
```

* `parentId`：同成员变量source
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

### UmlStateMachine

就是整个状态机。内含状态（State），Transition

### UmlRegion

链接到一个状态机

### UmlState

* `_parent`链接到一个`UmlRegion`

### UmlEvent

一个事件，作为Transition的trigger

* `_parent`链接到一个`UmlTransition`

### UmlTransition

* `_parent`链接到一个`UmlRegion`

### UmlRegion

* `_parent` 链接到一个`UmlStateMachine`

## Sequence Diagram

顺序图相关确保每个顺序图中，Lifeline和其Represent均一一对应。

collaboration 不输入

### UmlInteraction

抽象地表示了**顺序图**

* `parentId`链接到一个collaboration

### UmlAttribute

* `parentId`链接到一个collaboration


### UmlLifeLine

就是顺序图的参与对象

* `parentId`链接到一个`UmlInteraction`
* `represent`链接到一个`UmlAttribute`

### UmlMessage

* `parent`链接到一个`UmlInteraction`
* `targetId`链接到接收消息的对象
* `sourceId`哪个lifeline发的
* `messageSort`，表示消息的类型，同步的，还是reply

## 指令

### getType()

UmlAttribute和UmlParameter都有一个成员变量

```
private final NameableType type;
````

`getType()`后判断instance即可

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

## R00X

### R001


### R002

找到循环继承的类或接口，输出之。

类比较简单。

对于每一个类，为其建立一个chain，就是一条parentPath，dfs它，

dfs时，遍历generalization，有sourceId就加到parentPath里，如果在parentPath里又找到了自己，说明parentPath中存在circle，把circle取下放到返回值里。

### R003

接口不能重复继承同一个接口。

因为输入数据保证类的单继承，所以类不会出现重复继承。若类的继承链中有环，则R002会被触发，R003不做检查，直接异常。

接口的的继承应该是一颗多叉树。

接口的两个父亲的父接口集应该是无交集的。

每个接口通过Generalization更新自己的父接口集时，应该是对原有的扩充，不应出现重复。

### R004

类不能重复实现接口。包括类的继承，接口的继承。

每个类的继承，都应该是一颗多叉树。

### R005

> 目前类图已有的元素中，除`direction`为`return` 的`UMLParameter`、`UMLGeneralization`、`UMLAssociation`、`UMLInterfaceRealization`、`UMLAssociationEnd`以外，其余元素的`name`字段均不能为空。

在做element的加入时，对类图中除了`UmlAttribute`元素做一个pre check，具体来说，有

`UmlParameter`, `UmlOperation`, `UmlClass`, `UmlInterface`,

然后遍历用`ClassInteraction`中的`attribute`来做最后的抉择。

