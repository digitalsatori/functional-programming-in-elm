# 递归

大多数情况下我们无需自己编写递归函数。利用``List.map``函数来转换数据，利用
``List.foldl``函数来遍历数据并归集所需信息往往是更推荐的做法！

尽管这样说，但我们有时还是会碰到需要自己写递归函数的时候。我了解大多数的程序员会
**感觉到**\ 如何写这类函数，但这种无法言传的感觉是没法帮到初学者的。我回顾了一下我
写递归函数的心得，发现了我\ **一直用到**\ 的两个策略。希望会对你有所帮助

## 策略一 - 假设你已搞定

在写递归函数时，有两个重要的步骤：

  1. **以`case`子句开始** 分解数据，看看在每个分支你能获得什么。
  2. **假设你已搞定** 假设你的函数已经定义好了，直接拿来使用。

所以，假设我们没有``List.length``或``List.foldl``函数的情况下需要获取一个列表的长度。
第一步就要对这个列表（list)使用``case``判断：

```elm
length : List a -> Int
length list =
  case list of
    [] ->
      ...

    first :: rest ->
      ...
```

所以只有两种情况，第一种情况列表为空，那么列表长度就为0， 我们来把这个写出来。

```elm
length : List a -> Int
length list =
  case list of
    [] ->
      0

    first :: rest ->
      ...
```

另一种情况，我们把列表分解为``第一个``值和一个``剩余值``组成的列表。我们当然知道
一个值的长度是多少，那么``剩余值``列表的长度是多少呢？这时请记得第二步： **假设
已搞定** 。这时``length``函数应该其的作用，所以我们直接用就好了！

```elm
length : List a -> Int
length list =
  case list of
    [] ->
      0

    first :: rest ->
      1 + length rest
```

正如我们要花时间使得``for``循环对我们理所应当一样，上面的方法需要一个过程来适应
。唯一的方法就是不断的练习，说到练习...

> **练习题：** 定义一个 `sum` 函数来将列表中的整数相加。
>
> ```elm
sum : List Int -> Int
sum list =
  ...
```
>
> 记住： (1) 要使用使用 `case` (2) 假设你已搞定！


## 策略二 - 辅助函数

“假设你已搞定”策略超有用，但有时还不够。如果你发现还需要更多一些信息才能让那个策
略奏效时，正是辅助函数登场之时。下面来看看`reverse`(倒转)函数的设计：

```elm
reverse : List a -> List a
reverse list =
  ...
```

一个方法是将列表里的元素一个个分离出来并置入第二个列表：

![reverse](reverse.gif)

这就意味着在递归遍历第一个列表的同时要保持对 *第二个* 列表的跟踪。当需要额外的信息时，我们可以将递归分离到一个有更多参数的辅助函数中去：

```elm
reverse : List a -> List a
reverse list =
  reverseHelp list []

reverseHelp : List a -> List a -> List a
reverseHelp list reversedList =
  ...
```

`reverseHelp` 函数是一个递归函数，我们需要使用一贯的方法(1) 使用 `case` (2) 假设我们已搞定。所以我们先来分解列表`list`:

```elm
reverse : List a -> List a
reverse list =
  reverseHelp list []

reverseHelp : List a -> List a -> List a
reverseHelp list reversedList =
  case list of
    [] ->
      ...

    first :: rest ->
      ...
```

如果 `list` 为空则返回`reversedList`。如果 `list` 包含 `first` 元素, 则将其置于 `reversedList` 并继续：

```elm
reverse : List a -> List a
reverse list =
  reverseHelp list []

reverseHelp : List a -> List a -> List a
reverseHelp list reversedList =
  case list of
    [] ->
      reversedList

    first :: rest ->
      reverseHelp rest (first :: reversedList)
```

这样我们就实现了 `reverse` 函数!

这种 `foo` 联合 `fooHelp` 的模式是很常见的。`fooHelp` 部分可以让你在对一些数据结构做递归操作时可以保有额外的状态，而 `foo` 这部分函数则将这部分的细节对函数使用者隐藏。所以，`reverseHelp` 在递归过程中带上了一个额外的列表，而 `reverse` 则只有一个列表作为输入，在调用 `reverseHelp`时提供了一个初始的空列表 `[]`，这使得其API简洁。

正如第一个策略一样，最好的掌握该技能的方法是练习！

> **练习题：** 用策略而重新实现 `length` 函数。只改动 `lengthHelp` 函数部分！
>
>     length : List a -> Int
>     length list =
>       lengthHelp list 0
>
>     lengthHelp : List a -> Int -> Int
>     lengthHelp list lengthSoFar =
>       ...
>
> 当你搞定了上面一题，请用同样的方法实现 `sum` 函数。
>
>     sum : List Int -> Int
>     sum list =
>       ...
>
> 本练习中要求 `sum` 函数不能调用自身！


## 总结

在写递归函数时我们用到了两种主要策略：

  1. 使用 `case` 并且假设你已搞定。
  2. 创建一个 `fooHelp` 辅助函数用于保有额外的状态。

Both come up any time you need to write a recursive function yourself. That should be relatively rare given library functions like `List.foldl`, `Dict.map`, etc. but at least now you are better prepared for when the time comes!
虽然你真正需要写递归函数的情况很少，大多数情况下你都可以使用既有的函数比如
`List.foldl`, `Dict.map` 就可以搞定。 不过，真的需要写递归函数时，你也应该能用刚
学到的方法搞定了。

最后，这真的是要花一点时间才能用这种模式来思考问题的，正如你掌握 `for` 循环并习以为常。 **不要气馁！** 对每个人来说起初都是一样的，唯一正确的适应它的方法就是练习！

> **更多练习：** 练习递归函数的一个好方法是将 `List` 函数库里的所有东东都自己来实现一遍。比如说：
>
>  - [`member : a -> List a -> Bool`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#member)
>  - [`take : Int -> List a -> List a`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#take)
>  - [`drop : Int -> List a -> List a`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#drop)
>  - [`repeat : Int -> a -> List a`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#repeat)
>  - [`range : Int -> Int -> List Int`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#range)
>  - [`map : (a -> b) -> List a -> List b`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#map)
>  - [`map2 : (a -> b -> result) -> List a -> List b -> List result`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#map2)
>
> 试着对每个函数的实现使用上面的策略一和策略二。试着找到什么时候该用哪种策略的感觉。
