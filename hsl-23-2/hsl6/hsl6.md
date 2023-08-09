---
Title: "Exploring the Power of Cores"
Key Points:
- "A gate is a door with a single $ arm -- a core with a sample and a single $ arm"
- "We can create a gate with a custom sample with |: (barcol)"
- "Gate building doors are doors that take inputs and create a gate based on the inputs. They can represent an entire class of functions at once."
- "Hoon implements recursion by repeatedly building a core and running an arm with updated values."
- "A trap is a core with a single $ arm, which we can make with the |. (bardot) rune"
- "To make a core with a single $ arm and immediately run the arm, use |- (barhep)"
Runes:
- "`|=` (bartis)"
- "`|:` (barcol)"
- "`|.` (bardot)"
- "`|-` (barhep)"
Links:
- Bar runes: "https://developers.urbit.org/reference/hoon/rune/bar"
- Cen runes: "https://developers.urbit.org/reference/hoon/rune/cen"
- Hoon School Gates: "https://developers.urbit.org/guides/core/hoon-school/D-gates"
- Hoon School Cores: "https://developers.urbit.org/guides/core/hoon-school/F-cores"
- Hoon School Cores and Doors: "https://developers.urbit.org/guides/core/hoon-school/K-doors"
---

#   Exploring the Power of Cores
##  Hoon School Lesson 6

**Homework**: https://forms.gle/3y3v6h4hiH8tkiPQ8

**Video**:   https://youtu.be/LhO9WhFQWlk 

This is the second-to-last lesson of Hoon School, and the last one with big, new, important concepts. In this lesson we will continue to learn about cores and what we can do with them.

## Gates

Recall that in last lesson, we learned about cores and doors (which are just cores with a sample pinned to the head of the payload).

We started with a core with a sample, like the following code, which creates the following data structure.

![](Images/010.png)

 It has a battery (code stored as data) which is just one arm, containing `(gth n 10)`. It has a sample, which is the face `n` set to `0`. And it has a context which grabbed the dojo subject it was built in.


Then, we used `%=` (centis) to create a new core with `n` changed to a different value `11`. Then we called the `$` arm of this new core. This code returns `%.y` because in the new core, `n` is set to `11` and `11>10`
```
=>
=/  n  0
|%
++  $  (gth n 10)
--
=>
%=  .
n  11
==
$
```

Then, we simply changed the notation -- insteadly of explicitly writing the sample, we wrote the core as a door.

![](Images/030.png)

With the door, we can do the same operation of creating a new core with a different sample, then running the `$` arm.

```
=>
|_  n=@ud
++  $  (gth n 10)
--
=>
%=  .
n  11
==
$
```

Finally, we replaced the double operation of using `%=` and calling the `$` arm with a single operation of using `%~` (censig):

![](Images/040.png)

```
=>
|_  n=@ud
++  $  (gth n 10)
--
%~  $  .  11
```

This is looking a lot like a function -- we can substitute a value and then evaluate the code on it. In Hoon the purpose of a function is fulfilled by **gates**. So far we have used many gates such as `add`, `mul`, `gth`, and so on without knowing what's going on inside them. Now we will learn.

*A gate is simply a door with a single arm `$` (buc)*. But instead of writing out the door and the arm, we can define it more succinctly with the rune `|=` (bartis). 

![](Images/050.png)

We can pin this gate to a face `gth-10` and perform the same operations we did before. For example, we can call its `$` arm with a new sample using the `%~` rune. Note that even though we don't explicitly define the `$` arm, we can still refer to it.

```
> =/  gth-10
  |=  n=@ud
  (gth n 10)
  %~  $  gth-10  11

%.y
```

We can also do something completely equivalent that we learned before. We can use the `%-` (cenhep) rune.
```
> =/  gth-10
  |=  n=@ud
  (gth n 10)
  %-  gth-10  11

%.y
```

`%-` (cenhep) is exactly defined as calling `%~` (censig) on the `$` (buc) arm of a door. To make this correspondence even clearer, we can even use `%-` on something that's written as a door.

```
=/  gth-10
|_  n=@ud
++  $  (gth n 10)
--
%-  gth-10  11
```

In general, the `|=` (bartis) rune works like this:

![](Images/051.png)

The first argument is the type specification for the sample, and the second argument is a Hoon expression that goes in a single `$` arm of the core.

To tie it all together, these are the three equivalent representations of `gth-10`, as a core, as a door, and as a gate. They are all the same underneath, just different Hoon syntax.

![](Images/055.png)

## Gate Typing with ^-

When writing a gate, it's good ettiquette to use `^-` (kethep) to specify what the type of the output should be. It helps you know your gate is working correctly, it helps the compiler, and it helps anyone reading your code.

For example, in our `gth-10` gate,

```
|=  n=@ud
^-  ?(%.y %.n)
(gth n 10)
```

Recall that `?(%.y %.n)` is sugar syntax for `$?`, a type union over `%.y` and `%.n`.

## Investigating `add`.

Now that we know what gates are underneath, we can investigate some gates in the standard library. Since gate is just a core with a sample at the head of the payload, and a single arm `$`, we should be able to access these parts.

`add` refers to an arm in the core of the standard library. The `add` arm contains a gate which is not built yet until you call it. After building it, we can access its various parts.

![](Images/060.png)


Address `+2` retursn the battery. Recall if a core is at the `+1` address and it's a cell of `[battery payload]` the battery will be at the `+2` address.

```
> =>  add  +2
[6 [5 [1 0] 0 12] [0 13] 9 2 10 [6 [8 [9 2.398 0 7] 9 2 10 [6 0 28] 0 2] 4 0 13] 0 1]
```

This is the Nock code for adding two numbers -- don't worry about reading it.


Address `+6` returns the sample. If a core is at `+1` and is a cell of `[battery payload]`, then the payload is at `+3`. And if the payload is a cell of `[sample context]`, then the sample is at `+6` (some mental tree addressing may help).

```
> =>  add  +6
[a=0 b=0]
```

Address `+7` returns the context.
```
> =>  add  +7
<33.sam 1.pnw %139>
```

Calling the `$` arm of `add` computes it on the default values of `[a=0 b=0]`.
```
> =>  add  $
0
```

`%=` works to modify its sample and then run the `$` arm with the new sample.

```
> =>
  add
  =>
  %=  .
  a  10
  b   5
  ==
  $

15
```

`%~` can do the same thing.

```
> =>
  add
  %~  $  .  [10 5]

15
```

We have just uncovered what gates are underneath and how they work.

## Generators
Suppose we want to store a gate for later use, so that other programs can call on it, or we can call it from the Dojo. This is what's called a **generator**. Let's walk through creating and using a generator.

First, make sure your `base` desk (or any other desk) appears on earth by running `|mount %base`. Next, take the code for some gate, such as our `gth-10` gate from above.

```
|=  n=@ud
^-  ?(%.y %.n)
(gth n 10)
```

Open up a new file in your IDE and put the code in it, and save it as `gth-10.hoon` in the folder `zod/base/gen` (if you're using a fakezod). Then run `|commit %base`. You can now run your generator in the Dojo with:

```
+gth-10 11
```

Note that there's only one space after the `+gth-10`.

If you want to edit the generator, just edit the file and run `|commit %base` again.

If your gate takes more than one argument, like 
```
|=  [a=@ud b=@rs c=@t]
...
```

Just pass them in as a single noun, like
```
+mygenerator [1 .1.1 'abc']
```

## Gates with Different Default Sample Values

There are some reasons why you might want to build a gate with a different default value than the bunt of the sample type.

For example, recall that we learned about `roll`, which goes over a list and pairwise applies a gate to the entries in the list, reducing to a result. Here, roll multiplies up all the elements to get `1*2*3*4 = 24`

```
> (roll `(list @ud)`~[1 2 3 4] mul)
24
```

We also learned about floating point numbers, and that `mul:rs` is the gate to multiply two floating points.

```
> (mul:rs .1.234 .5.678)
.7.0066514
```

So here we expect it to return the result of all of the floating point numbers multiplied. Why does it return 0?
```
> (roll `(list @rs)`~[.1.234 .5.678 .2.222] mul:rs)
```

Let's build `mul:rs` and check what the default value of the sample is.

```
> =>  mul:rs  +6
[a=.0 b=.0]
```

It turns out that the default value of `mul:rs` is `[.0 .0]`


`roll` actually works by first working on the default values of the gate passed in. So it multiplies `.0` with `.1.234`, which results in `.0`. Then the final result is `.0` because `.0` times anything is `.0`.

To fix this, we can use the `|:` (barcol) rune. It's first child will be a Hoon expression result in a value. Then it infers the type of that value, and sets that type and value as the sample for the gate.

![](Images/080.png)

Note that unlike `|=`, the rune to make a regular gate, its first argument will be a value (in value mode), not a type specification (in structure mode).

Let's see it in action. This makes a new `mul:rs` gate with a new default values of `.1`. instead of `.0`.

```
=/  new-mul-rs
|:  [a=`@rs`.1 b=`@rs`.1]
^-  @rs
(mul:rs a b)
```

And we can use it in `roll` which now works correctly.

```
> =/  new-mul-rs
  |:  [a=`@rs`.1 b=`@rs`.1]
  (mul:rs a b)

  (roll `(list @rs)`~[.1.234 .5.678 .2.222] new-mul-rs)
  
.15.568778
```

## Gate building Doors

Recall that we could have cores with arms containing other cores. Of course this means we can have arms containing gates as well, since gates are cores.


Here we have a core with three arms, `double`, `triple` and `quadruple`, each of which are a gate that takes a number `a` and multiplies it.
```
|%
++  double   |=(a=@ (mul 2 a))
++  triple   |=(a=@ (mul 3 a))
++  quadruple   |=(a=@ (mul 4 a))
--
```

Composing this core with a call to `triple` builds a gate, which itself is a core 
```
> =>
  |%
  ++  double   |=(a=@ (mul 2 a))
  ++  triple   |=(a=@ (mul 3 a))
  ++  quadruple   |=(a=@ (mul 4 a))
  --
  triple

< 1.qmx
  [ a=@
...
```

Of course, we can use `triple` as a gate:

```
> =>
  |%
  ++  double   |=(a=@ (mul 2 a))
  ++  triple   |=(a=@ (mul 3 a))
  ++  quadruple   |=(a=@ (mul 4 a))
  --
  %-  triple  4

12
```

The outer core can be a door, and we can have the inputs to that door be used in the definitions of the inner gates. This is called a **gate building door**, and can be used to construct a general family of gates all at once.

For example, we are hopefully all familiar with linear equations like `y=ax+b`, where `a` denotes the slope of the line and `b` denotes where it crosses the vertical y-axis.

![](Images/090.png)

Let's build a door that can produce any linear equation as a gate. 

```
|_  [a=@ud b=@ud]
++  $  
  |=  x=@ud
  ^-  @ud
  (add (mul a x) b)
--
```

Let's go over the parts of this code to understand it.

![](Images/100.png)

![](Images/110.png)

![](Images/120.png)

![](Images/130.png)

![](Images/135.png)

![](Images/140.png)

![](Images/150.png)

Let's pin this to the head of the subject under the face `linear`, then use `%~` to call its `$` arm with a sample.

This builds a gate which represents the function `y=4x+2`.

```
> =/  linear
  |_  [a=@ud b=@ud]
  ++  $  
    |=  x=@ud
    ^-  @ud
    (add (mul a x) b)
  --
  %~  $  linear  [4 2]

< 1.yja
  [ x=@ud
...
```

Or in sugar syntax:
```
> =/  linear
  |_  [a=@ud b=@ud]
  ++  $  
    |=  x=@ud
    (add (mul a x) b)
  --
  ~($ linear [4 2])
```

After building a gate that corresponds to `y = 4x+2`, we can plug `x = 1` into it to get `y = 4*1+2 = 6`

```
> =/  linear
  |_  [a=@ud b=@ud]
  ++  $  
    |=  x=@ud
    ^-  @ud
    (add (mul a x) b)
  --
  %-  ~($ linear [4 2])  1

6
```

## Another Use of %=

We are now going to start learning about recursion in Hoon. Before we do so, there is a neat trick using `%=` that will be necessary to know for the next section. So far we have used `%=` where its first argument is a core, and it returns a core.

![](Images/170.png)

```
> =>
  =/  n  0
  |%
  ++  $  (gth n 10)
  --
  %=  .
  n  11
  ==

< 1.qet
  [ n=@ud
...
```

If the first argument is an arm, then it builds the core and immediately runs the arm too.

![](Images/175.png)

```
> =>
  =/  n  0
  |%
  ++  $  (gth n 10)
  --
  %=  $
  n  11
  ==

%.y
```


## Recursion in Hoon

Recall that in lesson 0, we studied a simple recursive function like this.

![](Images/180.png)

This function says, given an input `n`, if `n=0`, then return `n`. Else return the function computed on `n-1`. So if I give the function `n=3`, it will take the else branch and compute `function(2)`. Then what does `function(2)` compute? It takes the else branch and computes `function(1)`. `function(1)`  takes the else branch and computes `function(0)`. Finally `function(0)` returns `0`.

Let's understand such a function in Hoon.

```
=/  n  3
|%
++  $  
  ?:  =(n 0)
    n
  %=  $
  n  (sub n 1)
  ==
--
```

Let's analyze all the parts here.

First we pin `n` to the value `3`, which becomes the sample of the following core.

![](Images/190.png)

![](Images/200.png)

The core has a single arm `$`.

![](Images/210.png)

![](Images/220.png)

Recall that the `?:` (wutcol) rune says, if the first child produces `%.y`, take the second child, otherwise take the third child.

![](Images/230.png)

The first child of `?:` is the expression `=(n 0)`. Recall this is the sugar form of `.=` which returns `%.y` if two nouns are equal, and `%.n` otherwise.

![](Images/240.png)

If the fork produces `%.n`, then the code takes the second child and returns `n`.

![](Images/250.png)

Otherwise, we take the third child. Since the first input to `%=` is an arm, we build the core and run the arm with changes made.

![](Images/260.png)

![](Images/270.png)

This piece of code is just a core by itself. So if we type it and run it in the Dojo, it just returns the core. 

```
> =/  n  3
  |%
  ++  $  
    ?:  =(n 0)
      n
    %=  $
    n  (sub n 1)
    ==
  --

< 1.ibq
  [ n=@ud
...
```

But if we call the `$` arm of this core, it runs and returns `0`

```
> =>
  =/  n  3
  |%
  ++  $  
    ?:  =(n 0)
      n
    %=  $
    n  (sub n 1)
    ==
  --
  $

0
```

Let's go through what happened here.

![](Images/280.png)

![](Images/290.png)

![](Images/300.png)

![](Images/310.png)

![](Images/320.png)

![](Images/330.png)

![](Images/340.png)

![](Images/350.png)

![](Images/360.png)

![](Images/370.png)

![](Images/380.png)

![](Images/390.png)

![](Images/400.png)


If you have a core with a single arm `$`, that's called a **trap**. There is a rune you can use to write it faster, `|.` (bardot).

The rune takes a single child and puts it as the single `$` arm of a core.

![](Images/405.png)

So we could have written our above code more shortly. Instead of writing the `|%` and declaring the `$` arm, we simply use `|.`

![](Images/410.png)

Let's run it to make sure it does the same thing.

```
> =>
  =/  n  3
  |.
   ?:  =(n 0)
     n
   %=  $
   n  (sub n 1)
   ==
  $

0
```

If we have a core with a single arm `$` and we are immediately running that arm, then we can write it even more shortly with the rune `|-` (barhep). This is called building a trap and kicking it. `|-` is the rune you'll use most often for writing recursion in Hoon.

![](Images/420.png)

So we could have written our above code as:

![](Images/425.png)

See how we get rid of the `=>` and `$` because `|-` immediately runs the `$` arm for us.

Let's test the code:

```
> =/  n  3
  |-
   ?:  =(n 0)
     n
   %=  $
   n  (sub n 1)
   ==

0
```

What happens if we put `=/  n  3` inside the trap? 

![](Images/430.png)

Let's run it and see.

```
> |-
   =/  n  3
   ?:  =(n 0)
     n
   %=  $
   n  (sub n 1)
   ==

-tack.n
-find.n
```

![](Images/440.png)

Turns out we get an error message, no infinite loop. What's going on here?

Here we have written our above code in two equivalent ways, with `|-` and `|%`. Recall that when something is in an arm of a core, it's unbuilt code. Putting `=/  n  3` inside the trap puts it inside the `$` arm. So  `%=  $` can't find an `n` inside the core to change!

![](Images/450.png)

Here we show the two different positions of `=/  n  3` and where it ends up in the tree produced by the core. When it's in the payload (right branch), `%=` can find it because that's already built data.

![](Images/460.png)

## Using Traps

Now that we have used a simple trap to illustrate how traps work, let's do some more useful computations with traps. Let's write a trap to all up all the numbers from 1 up to any number.

```
> =/  num  5
  =/  total  0
  |-
    ?:  =(num 0)
      total
    %=  $
    num  (sub num 1)
    total  (add total num)
    ==

15
```

Running this code gives us `5+4+3+2+1=15`. What happened here?

![](Images/470.png)

![](Images/480.png)

![](Images/490.png)

![](Images/500.png)

![](Images/510.png)

![](Images/520.png)

![](Images/530.png)


We can put this trap in a gate to allow it to take custom inputs. This code returns the gate core rather than a number output.

```
> |=  num=@ud
  ^-  @ud
  =/  total  0
  |-
    ?:  =(num 0)
      total
    %=  $
    num  (sub num 1)
    total  (add total num)
    ==

< 1.rpq
  [ num=@ud
...
```

We can save this as a generator `add-nums.hoon` in `zod/base/gen` and then run `|commit %base` from the dojo, and then run it with

```
> +add-nums 5
15
```

There is a neat trick we can do to simplify the above code further. Recall that a gate is just a core with a sample and a single `$` arm. And a trap is a core with a single `$` arm. Instead of writing a trap in a gate, we can recurse directly into the hidden `$` arm of the gate.

![](Images/540.png)

Remember that any definitions used in the recursion, like `total`, must be defined outside the core that's being recursed into.

```
=/  total  0
|=  num=@ud
^-  @ud
?:  =(num 0)
  total
%=  $
num  (sub num 1)
total  (add total num)
==
```

Let's show one more useful example of recursion. It's common to use recursion to work over lists. Let's use a trap to add up all the numbers in a list of numbers.

```
> =/  numlist  `(list @ud)`~[50 100 2 3 49]
  =/  total  0
  |-
    ?~  numlist
      total
    %=  $
    total  (add total i.numlist)
    numlist  t.numlist
    ==

204
```

Here's the explanation of this code:

![](Images/550.png)

Recall what `|-` (barhep) means:

![](Images/570.png)

Recall that the `?~` (wutsig) rune says, if the first child is equal to `~`, run the second child, otherwise run the third child. Recall than an empty list is represented as `~`.

![](Images/580.png)

![](Images/590.png)

![](Images/600.png)

![](Images/605.png)

![](Images/606.png)

![](Images/607.png)

So on and so forth.

We could have also put this code into a gate so it can take any list of numbers.

```
=/  add-numlist-gate
|=  numlist=(list @ud)
^-  @ud
=/  total  0
|-
  ?~  numlist
    total
  %=  $
  total  (add total i.numlist)
  numlist  t.numlist
  ==
(add-numlist-gate ~[1 2 3 4 5 6 7 8])
```

With the gate, we can recurse into its hidden `$` arm and get rid of the `|-`, just as we explained earlier.

```
=/  add-numlist-gate
=/  total  0
|=  numlist=(list @ud)
^-  @ud
?~  numlist
  total
%=  $
total  (add total i.numlist)
numlist  t.numlist
==
(add-numlist-gate ~[1 2 3 4 5 6 7 8])
```
We can also save it as a generator `add-numlist.hoon` in `zod/base/gen`, commit, and then run it.

```
> +add-numlist ~[1 2 3 4 5 6 7 8]
36
```

## Tail recursion

We could have written the above code in an even simpler way. We can get rid of `total` by putting the recursive call inside of a gate call.

Can you figure out what's going on in the below code? Recall that

```
$(numlist t.numlist)
```

is sugar for

```
%=  $
numlist  t.numlist
==
```

```
|=  numlist=(list @ud)
^-  @ud 
?~  numlist
  0
(add i.numlist $(numlist t.numlist))
```

##  Polymorphic Cores (Optional Further Reading)
For those with previous functional programming or theoretical programming languages experience, you may like to check out how Hoon implements polymorphic (type generic) cores:

https://developers.urbit.org/guides/core/hoon-school/R-metals

## Conclusion

We have shown that cores can evolve into gates, which serve like functions in Hoon, and traps, which are used for recursion. Once you understand cores, their usage and flexibility, you've gotten Hoon's biggest concept. Next week we will wrap up HSL with topics such as exploring the standard library and writing production code.