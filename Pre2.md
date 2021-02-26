# Pre2

记录资料以及自己犯过的错。

## 参考资料

[CSDN: 多态](https://blog.csdn.net/yigan123/article/details/106504556)

[CSDN: 简单工厂](https://blog.csdn.net/u013132035/article/details/80633755)

### 自己的想法

对于这次课程，`ArrayList<SuperClass>`里面可以存`SubClass`，我感觉这是唯一的用处（我是不是没救了...)。

## Task3

1. 输出书本信息时，有两个属性之间没有用空格隔开
2. 简单工厂新建OtherS时，year未接受
3. 查询书架上丛书的种类个数，我的做法是遍历bookshelf，将bookset的类名添加到一个`ArrayList<String>`里，直到ArrayList的size为题设允许的最多丛书种类，结果在此处笔误了，打错成书架大小为丛书种类了。
   ![查询书本种类数量](img/查询书本种类数量.jpg)
4. 以为题设允许的最多丛书种类就6种，其实是7种。
   ![以为6种，其实7种](img/以为6种，其实7种.jpg)
