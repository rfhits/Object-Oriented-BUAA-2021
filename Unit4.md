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

特别注意官方包models/elements下的那些问价，就是UML类图的“组件”。

想要弄懂那些英文单词，可以看[这个](https://javapapers.com/oops/association-aggregation-composition-abstraction-generalization-realization-dependency/)。

本文章参考此[博客](https://www.cnblogs.com/puzzledAtticus/p/11076553.html)。

## 官方包解析

这玩意最好自底向上看。


### UmlParameter

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

### UMLAssociationEnd

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

### UMLAssociation

只是表示某种链接的关系。如`企鹅`和`气候`。

链接到两个UMLAssociationEnd的Id。

> * UMLAssociation
>   + end1：对应其中一个UMLAssociationEnd的Id
>   + end2：对应另外一个UMLAssociationEnd的Id

然后我们要通过Id获取到这两个UMLAssociationEnd，再通过End的getRef得到ClassId。

### UmlInterface

注意接口与接口之间的继承关系。

可以为接口维护自己的父接口，这样查询一个类对应所有父接口时可以快。

### UMLInterfaceRealization

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

### UMLGeneralization

表示继承，如`Person`和`Student`。

* UMLGeneralization
  * **_parent**：同source，对应子类ID
  * source：对应子类ID
  * target：对应父类ID
