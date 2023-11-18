---
Title: "Molds and Decision Making"
Key Points:
- "Structure mode is when the Hoon parser is talking about a type structure, and not directly about values."
- "Value mode and structure mode both use syntax a=b, but the first desugars to ^= and the second desugars to $= ."
- "Building and using molds with `$:` and `$?`"
- "The difference between casting with a mold and molding with it"
- "Basic decision making with `?:`"
- "Using comparison operators (gth lth gte lte .= )that return loobean (%.y or %.n) values"
- "NOT (?!) AND (?&) and OR (?|) operators in Hoon"
- "Switch statements over type unions, using `?-` and `?+`"

Runes:
- "`$=` (buctis)"
- "`$:` (buccol)"
- "`$?` (bucwut)"
- "`?:` (wutcol)"
- "`?!` (wutzap)"
- "`?&` (wutpam)"
- "`?|` (wutbar)"
- "`?~` (wutsig)"
- "`?-` (wuthep)"
- "`?+` (wutlus)"
Links:
- $ (buc) runes: "https://developers.urbit.org/reference/hoon/rune/buc"
- ? (wut) runes: "https://developers.urbit.org/reference/hoon/rune/wut"
- Hoon School Docs, Molds: "https://developers.urbit.org/guides/core/hoon-school/E-types"
- Hoon School Docs, Type Checking: "https://developers.urbit.org/guides/core/hoon-school/M-typecheck"
- Hoon School Docs, Conditional Logic: "https://developers.urbit.org/guides/core/hoon-school/N-logic"
- Aura reference: "https://developers.urbit.org/reference/hoon/auras"
---
#   Molds and Decision Making
##  Hoon School Lesson 3


**Homework**: https://forms.gle/Cd8WhRbHUaXWXwXD8

**Video**:  https://youtu.be/QKakP2EZYaQ

## Introduction to Molds

In last class, we went over how to use Hoon's type system for atoms (single values). We learned that atoms can be cast to each other if they nest in a tree-like structure like this

![](Images/010.png)

We learned how to use the `^-` (kethep) rune as a "fence" to make sure the result of a computation is a certain type

```
> ^-  @ud  %-  add  [1 2]
3
```

We also learned how to use the `^+` (ketlus) rune, which uses type inference on its first argument, to do the same thing.

```
> ^+  ~zod  %-  add  [1 2]
~wes
```

And we learned how to get the default value of an aura with `^*` (kettar) and its sugar syntax.
```
> ^*  @rs
.0

> *@rs
0
```

This is all great, but so far we have just been dealing with single values. For general computing, you would need types for more complicated structures: cells, tuples, lists, sets, maps, and so on.

Hoon covers these sorts of types with what's called a **mold**. To use a chemical analogy, if atoms are like atoms, molds are like molecules -- more complex structures that are built bit by bit out of atoms. Molecules can be simple, like water, or complex, like DNA, and the same goes for molds.


## Stucture vs Value Mode
When you are writing Hoon to create a mold, you are in what's called **structure mode**. Structure mode is when the Hoon parser is interpreting your code as a specification for a type, rather than directly writing something that computes a value.

Let's look at an example. In the code below, we are simply pinning the face `word` to the string `'hello'`, and then returning the face `word`.
```
> =/  word  'hello'  word
'hello'
```

 We could have also added a bit of type information to the face `word` to specify that `word` must be of type `@t`.

```
> =/  word=@t  'hello'  word
'hello'
```

It turns out that this is actually sugar syntax for the following rune, `$=` (buctis).

```
> =/  $=(word @t)  'hello'  word
'hello'
```

The `$=` rune takes as input two children -- the first of which is a face and the second of which is a specification for a type. In structure mode (when you're writing a type specification), it also sugars to `face=type`

![](Images/020.png)

Importantly, we've now seen two different kinds of syntax where we write something like 'a=b'. We have seen directly assigning a face to a value, such as:

```
> word='hello'
word='hello'
```
Or,
```
> a=[word='hello' num=2]
a=[word='hello' num=2]
```

In the last lesson, we learned that this is actually sugar for the `^=` (kettis) rune.

![](Images/035.png)

And if you'll recall, it can even handle sequential/nested face pinning like this

```
^=  [b c d=[x y]]  [1 2 3 4]
```

which would sugar like this:

```
[b c d=[x y]]=[1 2 3 4]
```

Alternatively, we can use this syntax in the way we just learned, to specify that a face has to have a certain type:

```
> =/  word=@t  'hello'  word
'hello'
```

It's very important to note that these two syntaxes, even though they look similar, desugar to different things, depending on whether you're in value mode (writing directly about values) or structure mode (writing a type specification).

![](Images/030.png)


## Building and Using a Simple Mold

Let's go through an example of building and using an actual mold using structure mode. Suppose we are building a simple bank app. We might represent accounts by `@p`, and for each account, we might want to store how much money they have in a `@rs` floating point. We would want these two values to be linked together -- each account has its specified amount of money.

To do so, we will use a new rune called `$:` (buccol). `$:` takes an indefinite number of children and creates a mold of a tuple of the type specified in each position. So it can have 2 children, and create a type that's a cell, 3 children and create a type that's a triple, and so on.

![](Images/040.png)

This code makes a new data type `bankaccount` and pins it to the subject of our Dojo:
```
=bankaccount $:  $=  ship  @p  $=  money  @rs  ==
```

To parse this, first we note the `$:` which means we are creating a cell type. Next, the subexpression `$=  ship  @p` is the first child of the `$:`. It says that the face `ship` must be something of aura `@p`. Next, the subexpression `$=  money  @rs` is the second child of the `$:`. It says that the face `money` must be something of aura `@rs`. The `$:` expression is closed by the `==`, to indicate there are no more children. Together, this expression creates a cell type which must have a `@p` in the head position and a `@rs` in the tail position.

Now let's check that our mold building worked. Certainly the following cell should fit this mold, right? The Hoon parser should know that the first entry is of type `@p` and the second entry is of type `@rs`. 
```
[~zod .123456.78]
```

We can use the `^-` rune in a way just like we did with atoms and auras.

```
> ^-  bankaccount  [~zod .123456.78]
[ship=~zod money=.123456.78]
```

The result with faces attached to entries of the cell indicates that casting this data with the mold worked.

For molds, the following sugar syntax is exactly equivalent to using `^-`:

```
`bankaccount`[~zod .123456.78]
```

Whereas if you'll recall for auras,

```
`@p`123
```

desugars to

```
^-  @p  ^-  @  123
```

We can substitute the definition of `bankaccount` into our statement to get the full statement:

```
> ^-  $:  $=  ship  @p  $=  money  @rs  ==  [~zod .123456.78]
[ship=~zod money=.123456.78]
```

We'd parse this statement in two parts, which are the two children of the `^-`, shown in the following graphic:

![](Images/050.png)


What is happening when we do this type casting? First, the Hoon compiler checks the first position in the mold, which we specified as `@p`. Then it checks the first position of the value, which is `~zod`, infers a type from it (which is `@p`) and checks that the inferred type `@p` nests into `@p`.

![](Images/060.png)

Then it checks the second position in the mold, which we specified as `@rs`, and makes sure that the inferred type of `.123456.78`, which is `@rs` nests into `@rs`, which it does.

![](Images/070.png)

Having passed all the checks, it returns the cell with with faces attached to it.


How about an example where it doesn't work? If we try the following, it will throw an error, because `@t` (the inferred type of `'hello'`) doesn't nest under `@p`. In this case, the `nest-fail` error is perfectly descriptive -- we have a `@t` and need a `@p`.

```
> ^-  bankaccount  ['hello' .123456.78]
mint-nice
-need.@p
-have.@t
nest-fail
```

Same but with sugar:

```
`bankaccount`['hello' .123456.78]
mint-nice
-need.@p
-have.@t
nest-fail
```

![](Images/080.png)


To reiterate the point, the following won't work either, because `@ud` doesn't nest under `@p` or `@rs`. In this case, neither of the values nest into their corresponding mold entries.

```
> ^-  bankaccount  [1 2]
mint-nice
-need.@p
-have.@ud
nest-fail
```

Sugared:
```
> `bankaccount`[1 2]
mint-nice
-need.@p
-have.@ud
nest-fail
```

![](Images/090.png)

![](Images/100.png)


In the wild, you'd probably encounter these sorts of mold definitions in a more readable way. We can apply sugar syntax, first for `$=` to get this:

```
^-  $:  ship=@p  money=@rs  ==  [~zod .123456.78]
```

Then for `$:` to get this:

```
^-  [ship=@p money=@rs]  [~zod .123456.78]
```
![](Images/110.png)



Now we must note an important and subtle point. Suppose we wanted to define a new mold using the `=/` rune, such as in the following perfectly valid code:

```
> =/  bankaccount  $:  ship=@p  money=@rs  ==
  ^-  bankaccount  [~zod .123456.78]
[ship=~zod money=.123456.78]
```

In this case, if we wanted to sugar the `$:` rune, you would have to use this slightly different syntax:

```
> =/  bankaccount  ,[ship=@p  money=@rs]  ==
  ^-  bankaccount  [~zod .123456.78]
[ship=~zod money=.123456.78]
```

![](Images/120.png)

Why is that? Why is there an extra `,` (com) in front of the square brackets?

The  `=/` rune usually expects a value for its second child. We should be comfortable with its usage in cases such as:

```
=/  numbers  [1 2]  numbers
```

in which case the `[1 2]` would desugar to the `:-` (colhep) rune.

```
=/  numbers  :-  1  2  numbers
```

So when we specify that the face `bankaccount` is pinned to a mold, the leading `,` differentiates it from being a value and makes it clear you are in structure mode, talking about a type.

On the other hand, `^-` expects a type specification as it's first child, so there's no ambiguity and the following is perfectly fine.

```
^-  [ship=@p money=@rs]  [~zod .123456.78]
```

![](Images/130.png)


## Casting vs Molding

Let's now see something interesting. What happens if we call the mold like this?

```
> %-  bankaccount  ['hello' .123456.78]
[planet=~dozpel-bicreg-famtul money=.123456.78]
```

It returned something! If we'll recall, when we tried the same thing with `^-`, it threw an error.

```
^-  bankaccount  [`hello` .123456.78]
```

What's going on here? The only difference is the first rune, and in the first case the rune was `%-`, which we have learned is for calling gates. It turns out that molds are gates, and any mold can be called as a gate, which gives different behavior than casting it with `^-`.


Recall that when we use `^-`, we're doing element-wise type checking to make sure each position in the cell matches each specification in the mold. When we call the mold as a gate using `%-`, we treat the input cell as a raw noun with no type information whatsoever. Then we force the atoms in the underlying noun to fit auras in the mold.

We could have even done this on the cell `[1 2]` that also didn't work before:
```
> %-  bankaccount  [1 2]
[planet=~nec money=.3e-45]
```

In this case `~nec` is the `@p` representation of `1` and `.3e-45` is the floating point representation of `2`. We can of course use sugar syntax for `%-` as well:

```
> (bankaccount [1 2])
[planet=~nec money=.3e-45]
```

These two usages of molds are respectively called **casting** and **molding**, and their differences are explained in the following graphic. In particular pay attention to the compile-time vs run-time difference, which means that casting with `^-` catches errors, while molding with `%-` may introduce errors. 

![](Images/140.png)

To see an example of a runtime error, let's try to mold something that doesn't fit. We know that the mold `bankaccount` describes a cell of two atoms. If we give it the triple `[1 2 3]` (which if you recall desugars to `[1 [2 3]]`), we get a runtime failure:

```
> %-  bankaccount  [1 2 3]
dojo: hoon expression failed
```

In short, **casting** is used as a fence to make sure that any data that goes past a point must match a particular type, and catches mismatches when the code compiles. **Molding** is an active action to make some data fit a type. They are different, and you might use both of these by first molding some data and then casting it.

##  Type unions
There is one more rune we should learn for making molds. Suppose for whatever reason you want to create a type that is either text OR a floating point number OR a date. This is what's called a type union, and you create it with the following rune `$?` (bucwut).

![](Images/150.png)

Let's try an example. We can pin this to the Dojo:

```
=newtype $?  @t  @rs  @da  ==
```

Now we can check that `newtype` allows those three types to pass, and not others. If we give it a date,
```
> ^-  newtype  ~2023.6.15
 ~2023.6.15
```
it passes, and if we give it a @ud,
```
> ^-  newtype  123
mint-nice
-need.?(@da @rs @t)
-have.@ud
nest-fail
```
it fails. If we pass it a `@t` or `@rs`, it passes, but the value defaults to formatting it as the last type in the type union, which in this case is `@da` (this is simply a quirk of the type system -- don't worry too much about it).

```
> ^-  newtype  'hello'
~292277024401-.1.1..00.00.00..0000.006f.6c6c.6568
```

```
> ^-  newtype  .123.45
~292277024401-.1.1..00.00.00..0000.0000.42f6.e666
```

In practice, type unions are not used much with auras -- they are most commonly used with the `@tas` `term` constants that we learned in last lesson. As you'll recall, a `term` is both a text of type `@tas` and a unique type in and of itself. Let's try an example.

Here we pin to the Dojo subject a new type `reptile`, which is either a `%snake` `%lizard` or `%crocodile` constant.

```
=reptile $?  %snake  %lizard  %crocodile ==
```

We could have also written it with the sugar syntax for `$?`:

```
=reptile ?(%snake %lizard %crocodile)
```

To use this type, we can cast the constant `%snake`:
```
> ^-  reptile  %snake
%snake
```

Sugar:
```
`reptile`%snake
%snake
```

and check that the constant `%elephant` does not pass:
```
^-  reptile  %elephant

```

We can call it as a gate too,
```

> %-  reptile  110.442.424.461.676
%lizard
```

```
> (reptile 110.442.424.461.676)
%lizard
```

But if the underlying integer doesn't cast to `%snake` `%lizard` or `%crocodile`, you'll get a runtime error:

```
> %-  reptile  1
dojo: hoon expression failed
```

```
> (reptile 1)
dojo: hoon expression failed
```


## Combining Type Unions and Cell Types

Now suppose you are running a reptile care facility and need to keep track of how much you have fed your reptiles this week. To do so, we can combine the runes we just learned.

In case we haven't pinned the `reptile` type definition to the Dojo, we can pin it again. Then we can pin a new type definition `reptile-tracker`, whose first entry is the type union `reptile` and whose second entry is a `@ud`.

```
=reptile $?  %snake  %lizard  %crocodile  ==
=reptile-tracker $:  species=reptile  meals=@ud  ==
```

We can use it to cast:

```
> ^-  reptile-tracker  [%crocodile 3]
[species=%crocodile meals=3]

>`reptile-tracker`[%crocodile 3]
[species=%crocodile meals=3]
```

And we can use it to mold:
```
> %-  reptile-tracker  [110.442.424.461.676 4]
[species=%lizard meals=4]

> (reptile-tracker [110.442.424.461.676 4])
[species=%lizard meals=4]
```

Although this was a simple example of combining a type union and a cell type, you can combine them with as much complexity as you want -- type unions of cell types, cell types which have other cell types as their entries, and so on are all possible. These two runes allow you to build as complex molds as you need.

## Bunting Molds

Like with atoms, we can get bunts, or default values for all the molds we just created, using the `^*` rune.

For a type union, `^*` defaults to getting the bunt of the last type in the union. Recall that `newtype` was a union of `@t` `@rs` and `@da`.

```
> ^*  newtype
~2000.1.1

> *newtype
~2000.1.1
```


The same logic applies for type unions of terms, returning the last type in the union.
```
> ^*  reptile
%crocodile

> *reptile
%crocodile
```

For a cell type, `^*` just gets the bunt for each part of the cell, recursively, and returns those in the cell. For example, for the cell type `bankaccount` we created earlier of `@p` and `@rs`, it returns a cell with `~zod` in the first place and `.0` in the second.

```
> ^*  bankaccount
[ship=~zod money=.0]

> *bankaccount
[ship=~zod money=.0]
```

So for a combination of cell type and type union, it does what you expect:

```
> ^*  reptile-tracker
[species=%crocodile meals=0]

> *reptile-tracker
[species=%crocodile meals=0]
```

## Making Decisions

Let's context switch away from molds. In computer programming, making decisions based on whether a condition is met is very fundamental and important. Hoon has a family of runes for doing so, the `?` (wut) family, which are straightforward to use and understand.

The ones we will learn in this lesson are:
```
?:
?!
?&
?|
?~
?-
?+
```

The first and most fundamental rune is the `?:` (wutcol) rune. It takes 3 children -- the first child is a Hoon expression that evaluates to `%.y` or `%.n`. The second child is an expression to evaluate if the first child is `%.y`, and the third child is an expression to evaluate if the first child is `%.n`. 

![](Images/170.png)

The simplest possible example usages of `?:` would be like this.

```
> ?:  %.y  1  2
1
```

Since the first child is `%.y`, we take the branch in the second child, and simply return `1`


```
> ?:  %.n  1  2
2
```

Since the first child is now `%.n`, we take the branch in the third child and simply return `2`.

Pretty straightforward, right? To learn some usages that are a bit more complex, we'll take a quick detour to learn some arithmetic comparison operators in Hoon.

### Comparison Operators

We will now learn about the standard library comparison operators:

![](Images/180.png)

They pretty much work like you'd expect.

`1` is not greater than `2`:
```
> %+  gth  1  2
%.n
```

`2` is greater than `1`
```
> %+  gth  2  1
%.y
```

`10` is not greater than `10`
```
> %+  gth  10  10
%.n
```

`1` is less than or equal to `2`
```
> %+  lte  1  2
%.y
```

`2` is not less than or equal to `1`
```
> %+  lte  2  1
%.n
```

`10` is less than or equal to `10`
```
> %+  lte  10  10
%.y
```

We should also make use of the `.=` (dottis) rune which if you'll recall, is an all purpose comparison operator, comparing two nouns and returns `%.y` if they are equal and `%.n` otherwise.

![](Images/185.png)

```
> .=  %-  add  [1 2]  3
%.y
```

Using sugar notation for both `%-` and `.=`,

```
> =((add [1 2]) 3)
%.y
```

This rune ignores type information and just tests noun equality:
```
> .=  ~wes  3
%.y

> =(~wes 3)
%.y
```

And is not only for atoms
```
> .=  [1 2]  [1 2]
%.y

> =([1 2] [1 2])
%.y
```

### Back to Decisions

Since all of `gth` `lth` `gte` `lte` and `.=` return a loobean (`%.y` or `%.n`), we can plug them into our `?:` rune to compose more complex expressions.

```
> =/  x  3
  ?:  %+  gth  x  2  
    'it was greater than 2'  
  'it was not greater than 2'

'it was greater than 2'
```

```
> =/  x  1
  ?:  %+  gth  x  2  
    'it was greater than 2'  
  'it was not greater than 2'

'it was not greater than 2'
```

You'll most commonly see and use this in the sugar form:

```
> =/  x  1
  ?:  (gth x 2)  
    'it was greater than 2'  
  'it was not greater than 2'

'it was not greater than 2'
```

You can put further computation in the branches rather than just directly returning a value.

```
> =/  x  1
  ?:  (gth x 2)  
    (add x 30)  
  (add x 20)
  
21
```

Hoon has the equivalent of `NOT` `AND` and `OR` -- basic logical operations that you'd expect in any programming language.

The rune for `NOT` is `?!` (wutzap).  It simply takes an expression that reduces to a loobean, and reverses the loobean.
![](Images/190.png)

```
> ?!  %.y
%.n

> ?!  %.n
%.y

> ?!  (gth 2 3)
%.y
```

Sugar syntax:

```
!(gth 2 3)
```


The rune for `AND` is `?&` (wutpam). It takes an indefinite number of children, all of which should reduce to a loobean, and only returns `%.y` if all of the children do.
![](Images/200.png)

```
> ?&  %.y  %.y  %.n  %.y  ==
%.n

> ?&  %.y  %.y  %.y  %.y  ==
%.y
```

Sugar:

```
> &(%.y %.y %.y %.y)
%.y
```

And you can have more complex logical expressions:

```
> ?&  (gth 2 1)  (gth 3 2)  !(gth 4 3)  ==
%.n
```

Sugar:
```
> &((gth 2 1) (gth 3 2) !(gth 4 3))
%.n
```



The rune for `OR` is `?|` (wutbar). It takes an indefinite number of children, all of which should reduce to a loobean, and returns `%.y` if *any* of the children do.

![](Images/210.png)

```
> ?|  %.n  %.n  %.n  %.n  ==
%.n

?|  %.n  %.n  %.y  %.n  ==
%.y
```

Sugar:

```
> |(%.n %.n %.y %.n)
%.y
```

And of course you can have more complex expressions:

```
> ?|  (gth 1 2)  (gth 2 3)  (gth 4 3)  ==
%.y
```

Sugar:
```
> |((gth 1 2)  (gth 2 3)  (gth 4 3))
%.y
```

### Branching on Null

The rune `?~` branches based on whether the first argument reduces to `~`. If it does, it takes the first code branch, else it takes the second.

![](Images/220.png)

### Mint-vain Error
However, what happens if we run the following code? You'd think it returns `'it was null'`.

```
> =/  a  ~
  ?~  a  
    'it was null'  
  'it was not null'
```

However, running it actually gives us an error, `mint-vain`. What does this error mean? The character `~` by itself is it's own special type. Because of this, the Hoon compiler knows that the type of `a` is just `~`, so it knows that the second branch of the `?~` can never be taken. A `mint-vain` error is thrown whenever you have a branching conditional and the Hoon compiler knows a branch cannot be taken, based on the type information.

However, we can confuse the compiler by casting the ~ to a naked atom `@`. A `@` can either be `0` (which is the same atom as `~` underneath), or some larger number, so the type system is no longer sure that you can't take a branch of the `?~`.

```
> =/  a  `@`~
  ?~  a  'it was null'  'it was not null'
'it was null' 
```

We can even just directly use a number, which is inferred by the compiler to a `@ud`, and gives the same behavior.
```
> =/  a  0
  ?~  a  'it was null'  'it was not null'
'it was null'
```

```
> =/  a  1
  ?~  a  'it was null'  'it was not null'
'it was not null'
```

 A `mint-vain` error is something that can sometimes pop up and confuse Hoon coders, so it's good to know why it happens.


### Type Branching
Using the type unions that we learned about earlier, we have access to more powerful branching tools. Recall that earlier we made the type:

```
=reptile $?  %snake  %lizard  %crocodile ==
```

which means that `reptile` is either type `%snake` or `%lizard` or `%crocodile`. We will use this with the `?-` rune, shown below. In case the graphic is information overload, we will go through an example.

![](Images/230.png)

With our reptile type pinned to our Dojo session,

```
=reptile $?  %snake  %lizard  %crocodile  ==
```

We can define `r` to be of type `reptile` assigned to the constant `%lizard`. The switch statement using `?-` takes `r` as input, and if `r` is type `%snake`, outputs `'im a snake`, if it's type `lizard`, outputs `'im a lizard`, and so on. Since `?-` has a variable number of children, the expression is closed with a `==`
```
> =/  r  ^-  reptile  %lizard
  ?-  r
    %snake  'im a snake'
    %lizard  'im a lizard'
    %crocodile  'im a crocodile'
  ==

'im a lizard'
```

And if we change what `r` is assigned to, then we can get different outcomes out of this switch statement.

```
> =/  r  ^-  reptile  %snake
  ?-  r
    %snake  'im a snake'
    %lizard  'im a lizard'
    %crocodile  'im a crocodile'
  == 

'im a snake'
```


If the switch statement has an extra branch that we can't take, because it's not in the type union of the input, we get a `mint-vain` error.
```
> =/  r  ^-  reptile  %snake
  ?-  r
    %snake  'im a snake'
    %lizard  'im a lizard'
    %crocodile  'im a crocodile'
    %alligator  'im an alligator'
  == 

mint-vain
```

We also get a `mint-vain` if we don't specify the value `%snake` is of type `reptile` before it's inputted to the switch statement.

```
> =/  r  %snake
  ?-  r
    %snake  'im a snake'
    %lizard  'im a lizard'
    %crocodile  'im a crocodile'
  == 

mint-vain
```


The `?+` rune is just like the `?-` rune, except you get to define a default behavior to perform instead of specifying every case.

![](Images/240.png)

Here we define a new type union called `animal`, which is a union of `%bird` `%bat` `%cow` `%sheep` `%rabbit`. Then we set `a` to be the constant `%cow` and cast it to the type animal. The switch statement defines outputs for `%bird` and `%bat`, but has a default for everything else.

```
> =/  animal  $?  %bird  %bat  %cow  %sheep  %rabbit  ==
  =/  a  ^-  animal  %cow
  ?+  a  'i cannot fly...'
    %bird  'i can fly'
    %bat  'i can fly too'
  ==

'i cannot fly...'
```

```
> =/  animal  $?  %bird  %bat  %cow  %sheep  %rabbit  ==
  =/  a  ^-  animal  %bat
  ?+  a  'i cannot fly...'
    %bird  'i can fly'
    %bat  'i can fly too'
  ==

'i can fly too'
```


This concludes our section on `?` runes and decision-making. The short intro to lists at the end of the live lecture will be covered in next week's notes.