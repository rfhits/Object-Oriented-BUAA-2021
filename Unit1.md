# Unit1

## homework1

猜测有人遇到0不输出，结果最后带个加号

```
in: 2*x+1
out: 2+
```

建议直接从homework3入手，Jay1Zhang学长有存2019春OOhomework的描述。

## homework2

cos/sin里面只放x，这点简直就是救星啊

## bug

sin(x^2) pow 重写getDer，以为return的是pow，x^n->x^(n-1)

addCos忘了写

-+分割的充分条件，包含前面非-+

clone随意写，严格说，本人根本不会写clone，然后出错了

poly的tostring没括号，输出时如果补上括号就对了，但是因为优先级认为×，以为某些核心组件出问题了，实际上，只要加上()


## 优化

const+0，0不必输出

任何一个poly和term，都有去除两端括号，直到正常
