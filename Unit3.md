# Unit 3

只对部分规格进行解读，帮助大家更好地理解课程组想让我们干什么。

强推一个叫做**Beyond Compare**的软件，相信大家在OS课上对它已经不陌生了。这个软件可以比较不同文件甚至不同文件夹，在OS写出bug的时候，和学长的一对比，大大提高debug效率。

为什么要这个软件？

1. 同一次作业中，课程组可能会对提供的规格进行更新，用该软件可以轻松比对课程组在哪些地方进行了改动
2. 在迭代的作业中，用该软件对比官方包，方便找出新增或者改动了了哪些要求。

## homework1&2

### Person

`value[]`和`acquaintance[]`一一对应。可以看作这个Person的每个acquaintance都有一个对应的value。

`isLinked()`：和输入的person是否有通路。自己和自己之间有通路。

`queryValue(Person person)`：根据输入的person，在acquaintance中查找，并返回对应的value。如果没找到，返回0，不抛异常。

`getReceivedMessages()`：返回Person类中，messageList中的前四个，如果不足四个，则返回整个messageList

### Message

建议先实现内部的成员变量，再自动生成get方法，最后会报错有unimplemented method，自动生产下，再补全就行。

### Group

维护该group的id以及一个person的set/list

`getValueSum()`：对于Group中的每个人，遍历在group中和ta邻接的人，有个权重的和。这个函数就是要每个人接邻的边的权重和的和，相当于两个for循环。

从数学上说，就是Group维护nodes中，所有edge的weight的和的两倍。

### Network

`contains(id)`：是否存在某个人的id为id

`addRelation()` 对一个图已经存在的两点之间添加一条有权无向边.

`isCircle()` 判断两点是否连通

`queryBlockSum()`求整个图中,连通分量的个数

`addToGroup(int id1, int id2)`：将Network中一个id1的person，添加到network中一个id2的group中。若该Group中人数超过1111人，不添加，直接返回。

`int queryGroupPeopleSum(int id)`：根据groupId，返回该Group内的人数。

`delFromGroup(int id1, int id2)`：将id1的person从id2的Group中删掉。

`addMessage(Message message)`：将一个原本不存在于Network的message添加到Network维护的messages中。

msg本身的性质：

若`type == 0`，表示点对点发信息，person1和person2必须是熟人了，两者之间应当linked‘

若`type == 1`，表示perosn1已经在message的群组中，要群发消息，群中所有人的socialValue要变化。

`sendMessage(int id)`：Network维护了一个message队列，这里面就是要发送的信息。信息可以点对点发送，这时message的成员变量person1和person2发挥作用，这个发送者和接收者的`socialValue`都要增加；如果是群发的消息那么perosn1和成员变量Group发挥作用，整个Group的人`socialValue`都增加。

## homework3

### Network

#### addMessage()

第293行，特别对emojiMessage做了异常检查，要求加入的emojiMessage的id必须contains。也就是对应生活中，不能发送一个emojiId不存在的emoji。有一个坑就是要用emojiId作为exception的构造参数。

#### sendMessage(int id)

357行，添加对**红包消息**和**emojiMessaeg**的支持，人的钱要变化或者emoji的heat要变。

群发的红包消息很奇怪，把钱分成n份，组里的人均分。结果是发红包的人损失n-1份的钱，因为自己留了一份，可能是发了红包**自己马上抢了回来**😅。

#### deleteColdEmoji(int limit)

441行，给定limit，删除流行程度小的emoji和相应的emojiMessage。注意删除时最好用迭代器。

#### sendIndirectMessage(int id)

俩连通的人之间通信，返回值是最短路径的长度。考虑红包消息和Emoji消息的情况。

1. 消息从list中消失
2. 返回值是最短路径的长度
3. 俩人的socialValue皆改变
4. 若是红包消息，钱要变
5. 若是emojiMessage，heat要变
6. 接收者的消息list里要放入这个message
