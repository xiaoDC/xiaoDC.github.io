---
layout: post
author: fri3nds
title: promise is a monad?
category: 技术博文
tag: [javascript, function programming, monad, functor, applicative]
description: "Promise 在隔离副作用上和 Monad 有异曲同工之妙，那 Promise 是 Monad 吗？"
---

# promise is a monad?

Promise 是很好解决 js 异步的方案。

## Monad 单子
Monad 是一个 FP 中的专有名词。
A monad is just a monoid in the category of endofunctors.
Monad 就是自函子范畴上的幺半群。


### Functor 函子
在范畴论中，函子是范畴间的一类映射。函子也可以解释为小范畴内的态射。

> 态射是范畴内对象之间的映射关系。函子与它类似，函子是范畴与范畴间的映射关系，也就是可以通过一个函子，把一个范畴映射到另一个范畴。

可以将 Functor 理解成一个容器!
我们用 js 中的一些东西来解释一下可能更清楚。

```js
const addThree = (x) => x + 3
const array = [ 2, 4, 6 ]
const mappedArray = array.map(addThree)
console.log(mappedArray)
// => [ 5, 7, 9 ]
```
![functor](/public/img/functor.png)
可以理解 Array 就是一个 Functor， 而 array 就是 Array 这个 Functor 的实例。

我们可以把 array 看成一个集合或者一个范畴。
- array 里面的 2、4、6，应用了 addThree 与 mappedArray 中的 5、7、9 一一对应；

当然 Array 得是一个 Functor 的话，它还得满足 Functor 的其他特性，这里就不说了。

简单来说，函子就是一个可以将 function 应用到函子 value 的一个容器。


### endoFunctor 自函子
把一个范畴映射到自身的函子叫做自函子。

假设我们现在把我们的范畴定义的更大一些，array 的元素是这个有理数集合，那么 Array 就是一个自函子。

现实中的自函子有哪些呢，挂钟就是一个自函子。


### semigroup(半群) 与 monoid(幺半群)
google到数学里定义的群(group): G为非空集合，如果在G上定义的二元运算 *，满足
```
封闭性（Closure）：对于任意a，b∈G，有a*b∈G
结合律（Associativity）：对于任意a，b，c∈G，有（a*b）*c=a*（b*c）
幺元 （Identity）：存在幺元e，使得对于任意a∈G，e*a=a*e=a
逆元：对于任意a∈G，存在逆元a^-1，使得a^-1*a=a*a^-1=e
```

如果仅满足封闭性和结合律，则称G是一个半群（Semigroup）；如果仅满足封闭性、结合律并且有幺元，则称G是一个含幺半群（Monoid）。

比如自然数这个非空集合G，加上 + 这个二元运算，就是一个幺半群。(满足封闭性、结合律，0 就是幺元)

单子(Monad)是这样一个自函子范畴：

- 其自函子对象是：
  M: C → C

- 有以下两种自然变换：
  - unit(X): I → M  X 是 C 中的对象，I 是 id 自函子
  - join(X): M × M → M

显然，在这个自函子范畴上构成了一个幺半群，这个幺半群的集合是所有自函子，其二元运算是由join决定结果的自函子复合。

在Haskell中的monad是这样的：
```haskell
class Monad m where
    fmap    :: (a -> b ) -> f a -> f b
    return  :: a -> m a
    (>>= )  :: m a -> (a -> m b) -> m b
```


## Promise 和 Monad
OK, 那 Monad js 中的 Promise 有什么关系呢？

haskell 中 Monad 可以被用来隔离副作用，Promise 在 js 中主要也是用来处理异步的，隔离副作用的，所以我们本能可以将二者联系起来。

把 Promise 理解成一个容器。
1. `Promise.resolve(5)` 或者 `new Promise((resolve, reject) => resolve(6)` 是不是就是 Monad 里面的 return 方法
2. `Promise.then` 可以近似理解成 Monad 里面的 `>>= ` 方法。

```haskell
(pure 10 :: Maybe Int) >>= \x -> return (x * x)
```

```js
Promise.resolve(10).then(x -> x * x)
```

Promise 和 Monad 不仅形似，而且神似。

那么再回过头讲讲 Monad。


## Functor、Applicative 与 Monad
haskell GHC 7.8 重新定义了之前的 Functor、Applicative 与 Monad 的关系。

Functor ⟹  Applicative ⟹ Monad

那么看看 Applicative 是什么。

### Applicative

Functor 的定义
```haskell
class Functor f where
    fmap :: (a -> b) -> f a -> f b
```

```haskell
class Functor f => Applicative f where
    (<*>) :: f (a -> b) -> f a -> f b
```
ps: `<*>` 就是 fmap

Functor 函子
  fmap :: (a -> b) -> f a -> f b

Applicative 应用函子
  (<*>) :: f (a -> b) -> f a -> f b

Applicative 必须是一个 Functor，而 Functor 只能将一个 value a 装进容器变成一个，然后与函数 a -> b 运算；
但是 Applicative 可以将函数 a -> b 装进容器，与原有的容器 f a 运算。

### Applicative 与 Monad 的区别
```haskell
[ x + y | x <- [1..3], y <- [1..x]]
-- [2, 3, 4, 4, 5 ,6]

[1..3] >>= \x -> [1..x] >> \y -> return (x + y)
-- [2, 3, 4, 4, 5 ,6]
```

```haskell
(+) <$> [1..3] <*> [1..x]
-- Not in scope: 'x'
```

y 的取值是依赖于 x 的，使用 Monad 是可以的，但是使用 Applicative 是无法做到的！

也就是说 Monad 后面的计算可以依赖于前面计算的结果，但是 Applicative 中的每个参数的计算是独立的，后面的结果不能依赖于前面的。
通俗一些说 Monad 可以表达 `上下文（context）` 的计算，Applicative 是不可以的。

Monad在计算的时候，后一个计算问题可以用到前面的参数，也就是说各个计算之间不是互相独立的，而是有依赖关系的。

## 总结
同样的 Promise 是没有上下文的。

```js
Promise.resolve([1, 2, 3])
.then(x => [1..x])
.then(y => (x + y))
```
ps: 上述 js 的例子部分是伪代码

个人觉得，从 Monad 的严格定义上， 很多约束条件 Promise 的实现都是没有满足或者没有严格满足，但是其形式及其相似。可以说 Promise is a monad；

然而，从 context 这个核心上来看，Promise 现有的实现不能满足。可以说 Promise is not a monad。

但是可以肯定的是 Promise 在隔离副作用上和 Monad 有异曲同工之妙。不知在确立 Promise 规范的时候，有没有借鉴 Monad！
