# Curry in Haskell

So today I am going to go through the function *curry* in Haskell. I am finding a hard time trying to understand it and this is my way of trying to understanding it.

## First the type signature
This is what I get when I type in `:t curry`
```haskell
curry :: ((a, b) -> c) -> a -> b -> c
```

So what I understand from this is that *curry* is a function that takes **3 arguments**. The first argument is a function and the rest are just values.

## The uncurried function
```haskell
((a,b) -> c)
```
The function here is what I am mostly interested in because unlike other kinds of functions in Haskell, it takes a tuple of arguments.
This tells me one thing - this function cannot be partially applied and both of the arguments must be supplied at the same time.

The other arguments `a` and `b` can be ommited because *curry* itself can be partially applied.

## First Conclusion
Furthermore, I can make a conclusion of saying that this how *curry*'s type signature should look:

```haskell
curry :: ((a, b) -> c) -> (a -> b -> c)
```

The result of applying a function that takes a **tuple** as its arguments to *curry* will result in a function that can be *curried*.

When I check how *curry* is defined in [Hackage](http://hackage.haskell.org/package/base-4.10.1.0/docs/Prelude.html#v:curry) the comment says that :

> "*curry* converts an uncurried function to a curried function."




But *curry* is defined like so in the Prelude:
```haskell
curry                   :: ((a, b) -> c) -> a -> b -> c
curry f x y             =  f (x, y)
```

How does *curry* expect to turn an **uncurried** function to a *curried* function by applying both arguments as a tuple to the **uncurried** function?

## More Investigation
This requires more investigation.
I will create my own **uncurried** function and pass it to *curry*.

```haskell
add :: (Int, Int) -> Int
add (x,y) = x + y
```
So `add` is a function that takes a takes a tuple of `Int`s and then adds them together.

I will then apply `curry` to `add`.

```haskell
curry add
```

The type of the resultant function is:
```haskell
:t curry add

curry add :: Int -> Int -> Int
```

The result of applying `curry` to `add` results in a function that takes two `Int`'s and returns an `Int`. Most **importantly** it is a *curried* function.

## Ray Of Hope
I think this is how the function application would look like based on the definition of *curry* in the Prelude:

```haskell
curry add x y = add (x,y)
```
Even though `add` itself is never changed, the way it is applied changes because *curry* gives it the ability to.

```haskell
let currAdd = curry add

:t currAdd
currAdd :: Int -> Int -> Int

let intermediate = currAdd 1

:t intermediate
intermediate :: Int -> Int
```
At this point `add` looks like so:
```haskell
add(1,y)
```
All `add` is waiting for now is another `Int` so that it can produce the result.

I now get it. *curry* changes how the function `add` is applied by giving it the chance of taking one argument at a time instead of both arguments at once. Now that's clever!

