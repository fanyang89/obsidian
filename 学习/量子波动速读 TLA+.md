## 参考链接

[The TLA+ Home Page](https://lamport.azurewebsites.net/tla/tla.html)
[Learn TLA+](https://learntla.com/)

## TLA+ 简介

包括图灵奖得主 Leslie Lamport 在内的多个计算机科学家都认为，采用[[形式化方法]]（Formal Methods）对系统进行形式化验证和分析，是构建正确、可靠和安全的软件的一个重要途径。

TLA+ 将一个系统抽象为一系列离散的事件

- 我们熟知的第一个数字系统是时钟，它以离散的滴答声模拟时钟；计算机也是数字系统，由一系列离散的程序步骤组成
- TLA+ 将离散的事件描述为状态变化，系统中的变量会因为事件从一个状态变为另一个状态

如果我们把系统中的一个状态描述为一个变量，那么状态机可以被描述为：

1. 系统所有的变量
2. 这些变量的初始值是什么
3. 哪些动作让变量的状态发生改变

TLA+ 的关键：

- 抽象系统的变量
- 描述变量的初始值
- 变量的转变关系
- 对每个关键路径进行校验

Lamport 自己也多次强调，TLA+ 始终不是可以实际运行的生产代码，主要是针对系统中关键部分进行建模并验证，让你从设计层面上就能提前发现问题。

## Hello world

```tla
EXTENDS TLC

(* --algorithm hello_world
variable s \in {"Hello", "World!"};
begin
  A:
    print s;
end algorithm; *)
```

> `EXTENDS` 是TLA+的 `#include` 模拟。`TLC` 是一个包括了 `print` 和 `assert` 的模块。顺便说一下，`print` 是唯一可以使用TLA+的IO，它是为调试目的而提供的。

这里唯一不同的是`A:`，这是标签。TLC将标签视为规范中的“步骤”；标签里的一切都同时发生
只有在标签之间，模型才能检查不变量和切换进程。
您不能在同一个标签中两次分配同一个变量。在大多数情况下，它在这里并不是很有用，但以后会非常重要。

## 语言基础

### Operators

> Operators are what you’d think of as a function in a programming language. They take arguments and evaluate to expressions.

定义一个 `MinutesToSeconds` 函数，输入 `m` 输出 `m * 60`

```tla
EXTENDS Integers

MinutesToSeconds(m) == m * 60
```

也可以

```tla
SecondsPerMinute == 60
```

### 分支

```tla
Abs(x) == IF x < 0 THEN -x ELSE x
```

### 值

没有浮点数。
布尔值：`TRUE` 和 `FALSE`

相等：`=`
不相等：`#`

与：`/\`
或：`\/`
非：`~`
例子：

```tla
Xor(A, B) == A = ~B
```

蕴含
对于 `A => B`

- 当 `A = TRUE` 并且 `B = TRUE` 时，`A => B` 为 `TRUE`
- 当 `A = TRUE` 并且 `B = FALSE` 时，`A => B` 为 `FALSE`
- 当 `A = FALSE` 时，`A => B` 恒为 `TRUE`
  也就是 `A => B` 是 `~A \/ B`
  `~B => ~A` 与 `A => B` 有着相同的真值表
