# Introduction to Functional Programming
One wise man once said to me when I asked him as to what functional programming is 
> **Once you understand it, you lose the ability to explain it to others**<br>

In one line it is a way of programming with functions as building blocks rather than objects as is the case with Object orienter programming

## What is it
* It is another programming paradigm
* It treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data
* It is a declarative programming paradigm, which means programming is done with expressions or declarations instead of statements
* It has origins in [lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus) created in 1930s by Alonzo Church
* It is not something new. Even Lisp supported functional programming

## Major characteristics (buzzwords) of functional programming
There are a multiple buzzwords associated with functional programming, whose only purpose is to make is sound very cool and difficult to grasp
* Immutability - The state of a variable never changes
* Pure Functions - For a valid input there is a valid output
* No Side effects - Function does not change the input in any way
* Higher Order Functions - Functions can be assigned to a variable, passed in another function and also used as a return type of a function
* Types - Type is like a superset of classes in OOP paradigm
* Composition - functions can be chained.
* Currying - Extension to composition. One function can take only 1 parameter at a time
* Lazy evaluation - expressions can be evaluated later from time of declaration
* Referential transparency - A function can be replaced by evaluated result and program works the same

## Seeing them one at a time

### Pure functions
Every function takes a valid input and **must** return a valid output. They only work on their input parameters and nothing else

```
var z = 10;
int add(x,y) {
   return x + y;
}

```
This is a pure function. It does not touch any function outside its parameters. Also check another property of this function. No matter how many times you call this function. It will return same output for same input set of `x` and `y`. There is another important advantage of pure functions.

> There is consistency

Another pure function can be

```
int getFive() {
	return 5;
}
```

This was pure as well. Let' see another case

```
int z;
void addNoReturn(x, y) {
    z = x + y
}
```
This is not a pure function as it does not return anything valuable, ans also has a side effect as it modified variable z. So another property of pure function is

> Pure functions can have no side effect

Now, you are thinking, what about functions like
```
addToFile(filename)
updateTable(sql)
writeToSocket(packet)
```
Yes they are all impure. And we still need them. Functional Languages cannot eliminate Side Effects, they can only confine them. So all your side effects are now intentional and easier to debug. The goal is to minimize and also segregate from rest of code

### Immutability
> There is no variable in functional programming but only constants even though we call them variable for historic reason

This really is the most difficult to grasp concept for most of us used with imperative style of programming. Most of us are used to writing

```
int x = 1;
x++; //or x = x+1;
```
This is a very common usage in most programming language. However, if you remember algebra `x` can never be equal to `x+1`. So functional programming make it illegal to do `x++`. As long as `x` is in scope its value cannot change. So how to we change a variable here. Well there are two ways
1. Multi-valued changes (like changing value at one particular index in an array) : It makes a copy of the record with values changed where needed. The important things is it does it efficiently. For ex., certain libraries like immutable.js treat array as a collection of pointers and copy then is just a matter of changing few pointers.
2. Single Valued change (like counter variables in loop) : Here too it makes a copy of it with value changed. One more thing to note here is functional programming avoid loops altogether

For ex: In imperative style, if you have to sum an array

```
sum = 0;
for (int i = 0 ; i< array.size() ;i++) {
   sum += array[i];
}
return sum;
```
As you see here we are mutating both i and sum. One way to eliminate this is recursion

```
int sum(int start, int end, List arr, int value) {
   if end <= start {
      return value;
   }
   return sum(start+1, end, arr, value + arr[start])
}
```
Also in some function languages like ECMAScript 6 or even in java 8, you have some functions like `map` and `reduce`, which can help eliminate looping.

For ex: to sum
```
sum = list.reduce((a,b) => a+b, 0)
```
Here reduce takes first parameter as a function which takes 2 params, an accumulator (`a`) and a array element (`b`). Second parameter of reduce is the initial value of accumulator
Let's say you wanted to take the sum of square of each number in a list
```
sum = list.map((i) => i*i).reduce((a,b) => a+b, 0)
```

### Higher Order Functions
This simply means function can act as other value. Pass it around to different functions or even use it as a return value of another function. Many languages do not support this directly but there has been ways wherein we managed to pass around functions. Like in C/C++, we have `function pointers`, while in Java we can use a `single function interface` as a means to same end.
However, functional languages provide direct support of using functions as values. Let's see an example in Scala

```
scala> var changeAndAdd = (x:Int ,y: Int, fn : Int=>Int) => fn(x) + fn(y)

changeAndAdd: (Int, Int, Int => Int) => Int = $$Lambda$3989/1721733575@1eb71177

scala> changeAndAdd(2,3, (x:Int) => x*x)
res14: Int = 13

scala> changeAndAdd(2,3, (x:Int) => x*x*x)
res15: Int = 35
```
As you can see we are passing a function as 3rd parameter to `changeAndAdd`. Let's see another example
```
scala> def sayHi(prefix: String) = (s : String) => prefix+ " " +s
sayHi: (prefix: String)String => String

scala> var hi = sayHi("Hi!")
hi: String => String = $$Lambda$3993/1737394043@2f13b8a1

scala> var hello = sayHi("Hello")
hello: String => String = $$Lambda$3993/1737394043@5c45930c

scala> hi("there")
res19: String = Hi! there

scala> hello("there")
res20: String = Hello there
```
Here sayHi returns a function which takes a string and returns a string

### Composition
We have already seen some examples of composition in previous section. In Functional Programming, functions are our building blocks. We write them to do very specific tasks. One way we can promote reuse is by treating small functions as bricks to build a wall and then use this walls to build our castle. In theory this way of combining smaller functions to achieve bigger functionality is the essence of composition.
Let's see a small example. We have two functions

```
isEven = value => value%2 = 0 // Returns if a number is even
isGreaterThan = (value, check) => value > check //return true if value is greater than check
square = value => value*value
```
Now let's see if we have a list and we wanted to get a list which has square of all even numbers in list

```
newList = list.filter(isEven).map(square).collect()
```
Now, let's say we wanted to only have values is are even as well as greater than 10 to be in the list

```
newList2 = list.filter(isEven).filter(isGreaterThan(10)).map(square).collect()
```
That's it. Let's see another from Javascript

```
var sum = a b => a+b
var square = x => x*x
```
Now let's say I wanted a function which adds 3 numbers
```
var s3 = a b c => a + sum(b,c)

```
Or, let's say I wanted a function to return (a+b)^2

```
var sumThenSquare = a b => square(sum(a,b))
```
So rather than creating two new functions we have reused the existing function to create new functions. That is cool

### Currying
Currying in layman's term is ability for a function which takes more than one parameter to only take one parameter at a time. This makes compositions much cleaner and easier to do. Different languages take different approaches to it. In some languages you have to rewrite the functions to use only one parameter. Like in javascript ECMAScript6
```
//Two param way
var add = x y =>x+y

//One param wy
var addCurried = x => y => x+y

add(2,3) // returns 5
addCurried(2)(3) // returns 5
```
In scala there is a way to pass `_` as one param to convert any function to a curried function
```
scala> def multiply(m: Int, n: Int): Int = m * n
multiply: (m: Int, n: Int)Int
scala> multiply(2,2)
res8: Int = 4
scala> var timesTwo = multiply(2, _:Int) //curried function
timesTwo: Int => Int = $$Lambda$3979/1419571728@26a23216
scala> timesTwo(4)
res9: Int = 8
```
### Referential transparency
It basically means that a function call can easily be replaced by a expression value without impacting the result. This is property of every pure function

### Types
A type system is a syntactic method for automatically checking the absence of certain erroneous behaviors by classifying program phrases according to the kinds of values they compute. Type is like a finite or infinite set in which an input and output can belong to. Even functions can be types in this paradigm. Mathematically speaking , for ex:
```
f: R -> N
```
this tells us that function “f” maps values from the set of real numbers to values of the set of natural numbers. Given these annotations, the compiler can now statically (at compile time) verify that the program is sound. That is, compilation will fail if values (at runtime) will not comply to the constraints imposed by the program.
Let's see an example function (pseudocode)
```
float divide(int number, int divisor) {
  return number/divisor
}
```
This looks fine. Until we call `divide(7,0)`. So now to handle this case
```
float divide(int number, int divisor) throws Exception {
  if dividor = 0 {
    throw SomeException
  }
  return number/divisor
}
```
Now this works, but this is not a pure function. Remember, pure functions returns a valid output for a valid input, and in this case the function tells any `int` is a valid input but it is throwing an exception if we pass 0. So, how to do we handle it. How about we create a new type, which contains all non-zero numbers and use that as a type for divisor. Some languages do provide you such a type, else you can create a class which does not allow to create an object with 0. Then this function remains pure.


## Advantages of functional programming
* Cleaner code (in most instances)
* Less bolierplate code
* Easy to test
* Easy to handle concurrency (Immutability, absence of state)
* Makes implementation of Lazy evaluation easy
* It makes you sound smart :)

## Disadvantages of functional programming
* Side effects are compulsory on many occasions
* Difficult to grasp
* Lazy evaluation can make performance calibration difficult
* CPU/Memory intensive as copying is needed to provide immutability. This makes it unsuitable for High performance software like games or browsers
* Lot of hyperbole surrounds functional programming. At times a simple for loop could handle situation better but still people make it unreadable by chains of function calls

## Popular functional languages
* Haskell
* Scala
* Elm
* Erlang
* Kotlin (More of OO with some functional support)
* Java 8 (partial support)
* Javascript (with certain libraries)

You can even do functional programming in Go, Python or even Java 7 using workarounds here and there.

## References/Further Reading
* [Twitter's Scala school](https://twitter.github.io/scala_school/)
* [Fighting spam with Haskell](https://code.facebook.com/posts/745068642270222/fighting-spam-with-haskell/)
* [Wikipedia](https://en.wikipedia.org/wiki/Functional_programming)
* [Recurse blog](https://codewords.recurse.com/issues/one/an-introduction-to-functional-programming)
* [Anjana Vakil: Learning Functional Programming with JavaScript - JSUnconf 2016](https://www.youtube.com/watch?v=e-5obm1G_FY)
* [Dr. Venkat Subramaniam: Functional Programming with Java 8 ](https://www.youtube.com/watch?v=Ee5t_EGjv0A)
