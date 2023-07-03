---
Title: "Introduction to Computing and Development"
Key Points:
- "Computer data is encoded in binary"
- "Information as data + interpretation"
- "What is a formal system?"
- "Why do formal systems matter for understanding computers?"
- "Understand what a function is, in the context of a formal system"
- "Simple recursion and time complexity"
- "Using an IDE"
- "Using Git"
- "Using a terminal"
---

#   Introduction to Computing and Development
##  Hoon School Lesson 0

**Homework**: https://forms.gle/UPqvpNLy1Ns3aXVs6

Video: TBA

## Administrative
We have a syllabus for the course at the following link. It's very detailed -- please check it for anything administrative you may need to know, or for a list of useful resources.
https://docs.google.com/document/d/1ceJFjEoIiappYq-j4KSsiUkdOFudUAkJ3fA3LNGjzvk

We have an office hours survey so that I can determine the best time to hold office hours. If you haven't filled it out, please do so: 
https://forms.gle/47G1Myo2VaiRmS1f8


## Roots of Computing
The origins of computing go all the way back to humanity's search for truth. You could say the desire to understand the world clearly and represent truth is an essential feature of the consciousness we possess.

For example, it seems like an unambiously true statement that:
> Madrid is in Spain.

It also seems like an unambiguously true assertion (taking the previous statement as true) that:
> If I live in Madrid, then I live in Spain

Aristotle was one of the first to try to formulate logical truth like this. 

![](Images/04.png)

The problem is that ordinary language is slippery -- most of the speech we use does not fit neatly into the buckets of logic. For example, if I say "chair" everyone may think they know what a chair is. But can you precisely define it? This person thought they could.

![](Images/05.jpeg)
![](Images/06.jpeg)


Is there something that we can start with as a foundation to represent knowledge? What is the basis of knowledge? The simplest possible thing we can do -- perhaps the foundation of knowledge itself -- is to make a distinction. Either it is true that I have red hair, or it is false. Either it's true that I ate an apple today, or I did not. Yes/no, night/day, black/white, hot/cold, etc.

The idea of two things in opposition is philosophically fundamental. In the Bible, the first thing God did was create light and separate light from darkness. In Taoism, there is yin/yang, the two primordial forces, from which we get 8 elements, 64 hexagrams, and so on. We see this across the world in humanity's knowledge traditions.

## Representing Data

So this brings us to the bit. A bit is a singular value, which can be 0 or 1, representing a simple binary choice. This is a basis from which we can create many, many things.

What can we do with this humble 0 or 1? We can create numbers. How might we represent numbers with 0s and 1s? Here's one way:

![](Images/20.png)

According to this encoding, we represent the number three by the binary sequence `[0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, ...]`
Is that a particularly good or efficient encoding? Probably not. But it is certainly unambiguous and therefore valid.


The standard way that we represent numbers using bits is called **binary representation**.

![](Images/30.png)

How does binary work? It's pretty similar to the decimal notation we are familiar with. The rightmost entry represents the 1's place -- a 0 means there is no 1, a 1 means there is a 1. Going left one hop, we get to the 2s place -- a 0 means there is no 1, a 1 means there is a 2. Going left again, we get to the 4s place -- a 0 means there is no 4, and so on. We add all these up to get our number.

![](Images/35.png)


What information does this sequence of bits represent? By what we just learned, we might be convinced that it represents the number 427,603.

![](Images/41.png)

However, not so fast. We didn't specify that we wanted to interpret the bits as a binary number. What if we use a different decoding scheme? Now it means the word MARS

![](Images/42.png)

And now it means the word CARS
![](Images/43.png)

So we learn an important lesson here -- what matters as much as the data itself are the rules that we apply to interpret it. We can interpret a sequence of bits in infinitely many ways. It can be a number, it can be words, it can be a data structure, it can be a picture -- it all depends on the rules you apply to interpret the data into something meaningful in your mind.

Interpted one way, all the data, all the binary bits that make up your computer, could be thought of as a really really big number. Your Urbit is a big number! Or it can be interpreted as an app that displays on your screen, a game, or a video chat with friends.

The important takeaway is that any possible information can be encoded in a binary sequence with a suitable decoding rule.


## Performing Operations

So we have gotten an idea of how information might be stored on a computer -- information that carries some meaning for us. However, a computer isn't a static lump of information. It operates, it calculates, it manipulates. 

A computer is something that operates according to clear, strict, pre-defined instructions to perform tasks. We can tell our friends ambiguous things like "please get me a glass of water", they can understand and fulfill them approximately. However when we're programming computers, this doesn't work. We need to be very precise. How do we explain and understand this?

We begin the study of **formal systems**. A formal system consists of:

* **Axioms:** Starting points of the system, facts that are assumed to be true.
* **Inference Rules** Operations that can be repeatedly applied starting from the axioms to build new "truths"
* **Theorems:** Anything that can be reached by repeatedly applying inference rules starting from the axioms.

Why is it important to study systems like this? Well, suppose you can say the axioms correspond to all possible starting points of a computer, and the inference rules to the possible actions it can take. Then the set of theorems of the formal system precisely describes every possible state of the computer. This gives us correspondences such as:

This formal system **has a theorem** with X property <=> This computer **can reach a state** with X property

*For example, you can prove that your computer can perform some computation*

This formal system **has no theorem** with Y property <=> This computer **cannot reach a state**  with Y property

**All theorems of this formal system** have Z property <=>  **All states of this computer** have Z property

*For example, you could prove your computer cannot crash under certain circumstances*


This means that formal systems are extremely powerful tools for modeling and reasoning about computers -- you might say a formal system is exactly what a computer is, in a Platonic (idealized) sense.


More generally, returning to humanity's search for knowledge and truth. IF you believe that your formal system accurately corresponds to some aspect of the world, you can say things like,

<p style="text-align: center;">This formal system <b>has a theorem</b> with X property <=> ~X property <b>is true</b>  about the world</p>

<p style="text-align: center;">This formal system <b>has no theorem</b> with Y property <=> ~Y property <b>is false</b>  about the world</p>


To make this more concrete, let's look at an example of a formal system.

![](Images/53.png)

Can you come up with some theorems in this system?

![](Images/54.png)

**Hard Challenge:** Is the string "MU" a theorem in this system? Why or why not?

Here is another example of a formal system.
![](Images/55.png)

Something interesting about this system is that we don't have one axiom, like in the MIU system, we actually have an infinite number of axioms. In general, this is fine, as long as it is specified.
> Axioms of the p-q system:
>
> -p-q--
>
> --p-q---
>
> ---p-q----
>
> ...

Some example derivations in this system:

![](Images/57.png)

While this may look like a bunch of meaningless hyphenation, this system actually has an interesting property. In particular, for every axiom and theorem, count the number of hyphens before p, between p and q, and after q.

**Fact:** All theorems of this system have the number of "-" before p, plus the number of "-" between p and q, equal to the number of "-" after q.

**Another Fact:** For every true arithmetical statement of the form `x + y = z`, there is a unique theorem of the `p-q` system with `x` hyphens before p, `y` hyphens between p and q, and `z` hyphens after q.

We have modeled a simple computer which, under a certain interpretation, outputs all true facts about adding two positive whole numbers. Now the formerly meaningless symbols 'p' and 'q' may come to mean in our minds 'plus' and 'equals'.

However, we must be careful to not go too far.

> -p-p-q---

is not a theorem of the system, even though it's true that `1+1+1=3`. It's impossible to produce a string with two 'p's. So we must not take an interpretation farther than the underlying computation allows us.

## Variables and Functions
Let me show you one more formal system. Its theorems look like binary trees of numbers and the words `add` `sub` `mul` and `div`. In particular, here are how the axioms are defined:

![](Images/60.png)

For reference, here are some valid axioms:
```
6

157

-2.5999

   mul
  /   \
 2     3

       add
      /   \
    mul    5
   /   \
  2     3
```

Here is the full specification of the system. In short, whenever we have a subtree whos head is `add/sub/mul/div` and branches are <u>numbers</u>, we can collapse the subtree to just a number produced by performing the corresponding mathematical operation.

![](Images/62.png)

An example of a derivation in this system:

![](Images/63.png)

So we have created a simple system which models arithmetic.

Let's zoom out a bit. What exactly is a computer? You sit down to a computer, and there's a lot of possible things you can do. The computer does not know what you will do next. You might click on this button or that button, you might put some value in a form. It's job is to produce an appropriate response for any possible input you might give it. So you might think of a computer as a process which maps inputs to outputs.

So we have a process which is indeterminate while waiting for an input, and upon receiving one, performs a mechanical series of operations to produce an output. How can we model this in a formal system? How about this? 
```
       add
      /   \
    mul    5
   /   \
  x     3
```

What is the value of this? Can we reduce it using our rules? Remember that the reduction rules specify that the branches have to be numbers, and `x` is not a specific number. So this expression formally has undetermined value.


If you give it a value for x, THEN we can apply our rules to reduce it. And for every possible numerical value you can assign x, this function returns a particular output.

![](Images/66.png)

Having just been introduced to variables and substitution, we note that they don't just apply to numbers. We can also substitute whole expressions for variables:

![](Images/67.png)

Having performed substitutions and bound all the variables to values, we can now reduce the tree using our rules.

![](Images/68.png)

Before, we substituted `x` into `y` and then `9` into `z`.
We can also do the substitutions in the other order -- first substituting `9` into `z`, then `x` into `y`, and get the same result:

![](Images/69.png)


## Functional Programming

So why did we do all of this? It turns out that I have just introduced you to functional programming. Functional programming is a way to think about programming where everything is a function -- map from inputs to outputs. Here are the arithmetic diagrams we just covered, written in a functional language pseudocode. 

```
fun1(z):
  z - 1

fun2(y)
  y * y

fun2(fun1(z))

fun2(fun1(9)) -> 64
```

`fun1` and `fun2` are functions, mapping inputs to outputs.Their composition -- `fun2` ran on the output of `fun1`, is also a valid function. And when we plug in `9` to this composed function, we get the output `64`, just as before.


Many of you may already be familiar with programming in an Imperative language, such as C or Python. In this paradigm, the computer has a state, and you tell it various commands to operate on the state. If you want to combine multiple operations, you sequence them one after another. If you think this way about a functional program, you will get confused.

![](Images/72.png)

In functional program, you must concieve of of everything as functions from inputs to outputs. If you want to you combine operations, you pipe them -- you feed the output of a function as the input to another function, to create a new function. An Urbit instance is a function!



## Recursion 

Functions can call other functions in their definitions. We can rewrite our previous operation like this:

```
fun1(z):
  z - 1

fun2(y):
  fun1(y) * fun1(y)

fun2(9) -> 64
```

But, trickily and interestingly, functions can even call themselves in their own definitions. This is a very important concept called recursion. 

Consider this program:
```
function(n):
  if n = 0:
    return n
  else:
    return function(n-1)
```

Let's figure out what this function does. On an input of 0, the `if` branch triggers, and returns 0. What happens on an input of 1? In that case, the `else` branch triggers, and we return `function(1-1)` which is `function(0)`. We just checked that `function(0) -> 0`, so we have `function(1) -> 0` as well. Continuing with this reasoning, we can confirm that this function returns `0` for all positive whole numbers.


Recursion can also have a danger. Let's consider this function. 

```
function(n):
  if n = 0:
    return n
  else:
    return function(n+1)
```
What happens on an input of `0`? The `if` is triggered, and it returns 0. Now let's try to understand function(1). What happens?

## Time complexity

In programming, it's important to consider how many operations it takes to compute a result. Returning to our previous example, we may try to count how many calls to the function it takes to return. 

```
function(n):
  if n = 0:
    return n
  else:
    return function(n-1)
```
For an input of 0, it takes 1 function call to return. For an input of 1, it takes 2 -- the original call, and 1 call to compute function(0). For an input of 2, it takes 3 -- the original call, and 2 calls to compute function(1). 

So in general we can say this takes `n+1` function calls to return on an input of `n`. In computer science, we can say this function computes in `O(n)` time. For an input of size n, it takes a time to finish that scales up by a factor of n. (Note that recursing n times doesn't always mean a function takes O(n) time to compute -- it may take longer depending on the operations in the recursion).


Let's consider this new function. It's a bit trickier.

```
function(n):
  if n = 0
    return n
  else
    return (function(n - 1 ) + function(n-1))
```

 First, what does it do? Let's try `function(0)` first. It triggers the first `if` and returns 0. Now let's try `function(1)` It skips the `if` and goes to the `else`, returning 
 ```
 (function(1 - 1) + function(1 - 1)) = (function(0) + function(0))
 ```
 From our previous calculation, we know `function(0) -> 0`. So therefore `function(1) -> 0 + 0 = 0`

 Continuing with this reasoning, we can see that this function returns 0 for every positive whole number input.So it turns out this function does the exact same thing as our last one! 
 
 However, there's an important difference. How many calls to the function does it take to compute `function(0)`? Just 1. How about `function(1)`? It takes 3 calls, the original call and `1+1` calls to compute `function(0)` twice. How about `function(2)`? That takes 7 calls, the original call and `3+3` calls to compute `function(1)` twice. Computing `function(3)` takes `1+7+7=15` calls.
 

In general, this function takes `2^(n+1) - 1` calls to compute `function(n)`. So although they compute the same thing, the original function took time that grew linearly in n, while this new one blows up exponentially.

We have finished covering the important basics to understand functional programming and start learning Hoon.

## Introduction to Development

### Development Environment
To write software, you need an ability to write text and save it as a file. Technically, you could simply write in a simple text editor, but that wouldn't have many affordances to help you write code. Most people use software called an IDE or Integrated Development Environment. There are many options on the market, including VSCode, Vim, Emacs, Xcode, and others.

If you don't have a personal preference, we like to use VSCode (or VScodium to go open source) in this course. This is because it has several plugins for Hoon that will greatly help you for writing code.

For official Hoon support, including syntax highlighting, use this plugin: https://marketplace.visualstudio.com/items?itemName=tloncorp.hoon-tloncorp

Hoon Assist is a plugin that can show you definitions of runes and functions when you hover over them: https://marketplace.visualstudio.com/items?itemName=nocsyx-lassul.hoon-assist

And here is another syntax highlighter you can try: https://marketplace.visualstudio.com/items?itemName=urbit-pilled.hoon-highlighter

If you use VSCodium, you will have to download the .vsix file and then manually install it by going to Settings -> Extensions -> ... -> Install from VSIX.



Users of Vim and Emacs are welcome to try the following extensions, but be warned that there are reports that the install process is buggy or out of date:

https://github.com/urbit/hoon.vim

https://github.com/urbit/hoon-mode.el

For further help on learning how to use VSCode or any IDE, there are plenty of resources available online.

### Git

Git is an indispensable tool for programmers. It lets you upload your projects, back up them up, share them and collaborate with others, and store the entire version history, so you can go back and forth across time.

If you haven't used it before, you should make an account on Github, make a repo, and try out some basic operations. This will be a homework question.

There are many resources online for learning how to use git. I searched around and found a few links which can help you get started with it:

https://docs.github.com/en/get-started/quickstart/hello-world

https://github.com/git-guides

https://rogerdudler.github.io/git-guide/


##  Terminal

The last software development skill we want to cover is using  the terminal, or command line interface. Most computer users interface with the GUI, or the graphical interface. This is what we typically interact with, with icons representing files and folders and so on.

Although using the terminal is more challenging at first, learning it greatly unlocks your ability to quickly manipulate your computer. Here are a basic set of important skills:

* Seeing what files are in a directory
* Navigating between directories
* Making a new directory
* Moving a file from one place to another
* Copying a file from one place to another
* Deleting a file

There are many resources online to help you learn these skills. The details will differ depending on whether you use Linux/Mac or Windows.
