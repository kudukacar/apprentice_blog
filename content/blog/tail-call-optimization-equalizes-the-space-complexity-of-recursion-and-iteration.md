---
path: /blog/tail_recursion
date: 2020-03-30T12:48:45.343Z
title: >-
  Tail call optimization equalizes the space complexity of recursion and
  iteration 
description: A blog about tail recursion
---
I recently learned Elixir, a functional programming language. Due to immutability, Elixir uses recursion (i.e. a function that calls itself) instead of iteration (i.e. for or while loops) to repeatedly execute a set of instructions. In imperative languages such as Ruby, although recursive solutions appear concise and elegant, they perform worse than iterative solutions. An iterative solution takes up constant space, O(1), while a recursive solution takes up linear, O(n), space.

Most languages use a stack to keep track of function calls. Each function call gets a frame containing the function’s local data. Once the function finishes executing, the function is popped from the stack. Consider the following example.

```ruby
defmodule to_integer(input) do
 String.to_integer(input)
end
```

A call to function, to_integer(5), puts this function as the first frame on the stack.

![](https://docs.google.com/drawings/u/0/d/sN4B68A22K5_9SjoaSq5cLw/image?w=138&h=262&rev=3&ac=1&parent=1Mek02cn627zsGkD3hQvrnJpKVkeV6IWZ1LozwDuSOvM)

The function, to_integer, calls String.to_integer, adding another frame to the stack.  

![](https://docs.google.com/drawings/u/0/d/s4F_-bMqwfTLLPVhMsNFpSQ/image?w=138&h=262&rev=44&ac=1&parent=1Mek02cn627zsGkD3hQvrnJpKVkeV6IWZ1LozwDuSOvM)

Once String.to_integer finishes executing, the function pops off the stack. After to_integer finishes executing, the function pops off the stack.

Consider the following factorial recursive function.

```ruby
defmodule factorial(number) when number == 1 do
 1 
end
 
defmodule factorial(number) do
 number * factorial(number - 1)
end
```

A call to factorial(5) results in the following stack.

![](https://docs.google.com/drawings/u/0/d/sZxo-FPtoeimo4vuSVLSY-w/image?w=120&h=248&rev=109&ac=1&parent=1Mek02cn627zsGkD3hQvrnJpKVkeV6IWZ1LozwDuSOvM)

The function, factorial(5), does not finish executing until factorial(4) executes, and this function does not finish executing until factorial(3) executes. This dependency continues until factorial(1) executes. Once factorial(1) executes, it pops off the stack followed by factorial(2) through factorial(5). These functions remain on the stack because each depends on the value of the other, and don’t finish executing until reaching the base case (i.e. factorial(1)). This factorial recursive function has a space complexity of O(n), directly proportional to the number of recursive calls.

Elixir implements tail call optimization to minimize memory usage with recursion. Tail call optimization reduces the space complexity of recursion from O(n) to O(1). A tail call recursive function does not add frames to the stack with each recursive call. Instead, the same stack frame is used for all tail recursive calls, and local variables are replaced with each call. In a tail recursive function, the recursive call is the last statement in the function. In the factorial example above, the recursive call multiplied by number forms the last statement. This computation creates a dependency on the next recursive call, and therefore this function does not qualify as tail recursive.

Consider the following example, where we convert factorial to a tail recursive function, tail_factorial.

```ruby
defmodule tail_factorial(number, product = 1) when number == 1 do
 product
end
 
defmodule tail_factorial(number, product = 1) do
 tail_factorial(number - 1, product * number)
end
```

In this tail recursive function, the last statement is the recursive call without any added computation. Each call to tail_factorial does not rely on the value of the previous call, and has the product, the return value of the previous call, captured as an argument in the next call. Each recursive call has all the information it needs, eliminating the need to store information about the previous call. The previous stack frame with new local variables is reused for the current call. Hence, each successive recursive call does not add to the stack. See below a depiction of the tail_factorial call stack.

![](https://docs.google.com/drawings/u/0/d/s_qwX957C_mWAorHxuPoJEw/image?w=120&h=232&rev=27&ac=1&parent=1Mek02cn627zsGkD3hQvrnJpKVkeV6IWZ1LozwDuSOvM)

Tail call optimization allows for space efficient recursion while maintaining its benefits of conciseness and elegance.
