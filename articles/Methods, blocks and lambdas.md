# Methods, blocks and lambdas

*The different ways in which executable code can be stored and called, and which way is best for code-golf.*

------

- [Methods](#methods)
- [Blocks, lambdas (and procs?)](#blocks,_lambdas_(and_procs?))
- [Shortening maps by using `&` and `to_proc`](#shortening_maps_by_using_&_and_to_proc)

---

## Methods <a name="methods"></a>

In the previous article, methods were the type of procedure used to execute code. In all object-oriented programming languages, methods are undeniably the most popular way of executing code. However, Ruby offers another alternative to methods, that is **blocks**.

Despite *block* sounding like a very general term, they are a significantly different feature to methods, despite the fact that both of them do store what most people would call blocks of code.

In code-golf, **your solution should never be in the form of a method**. Lambdas save quite a number of bytes over methods, as you will see below.

## Blocks, lambdas (and procs?) <a name="blocks,_lambdas_(and_procs?)"></a>

Blocks are denoted by a section of code enclosed within `{ ... }` or `do ... end`, where `…` represents the code. In code-golf, you'll almost always want to use `{ ... }` because it saves bytes. However, note that `{ .. }` does have higher precedence than `do ... end`.

Note that blocks mean nothing when used as a standalone, like `{puts 'Block!'}`. And as such, it wouldn't make sense to directly assign a block to a variable:

```ruby
block = {puts 'Block!'}

#=> syntax error, unexpected tSTRING_BEG, expecting keyword_do or '{' or '('
#=> block = {puts 'Block!'}
#=>                 ^
#=> syntax error, unexpected '}', expecting end-of-input
```

Blocks can only be passed into methods, and can also have parameters (as seen with the `map` example below):

```ruby
3.times {print '3'} # 333
[1,2,3].map {|x| x**2} #=> [1,4,9]
```

What if we need to store blocks? This is where lambdas and procs come in.

[Procs and lambdas do have their differences](http://awaxman11.github.io/blog/2013/08/05/what-is-the-difference-between-a-block/), but most of the time you can use either without any trouble. However, generally lambdas are more useful in code-golf for one reason. You guessed it, bytes!

```ruby
# 40 bytes
a = Proc.new {puts "This is code-golf!"}

# 38 bytes
b = lambda {puts "This is code-golf!"}
```

So we saved two bytes, but it gets better. There is an alternative way of declaring a lambda, known as the *stab* operator, or the *stabby lambda*, denoted by `->`. Note that parameters in the stabby lambda are defined outside of the `{}`. The parentheses are optional though. And you can also define lambdas without parameters:

```ruby
c = ->(param){puts param}
d = ->param{puts param}
e = ->{puts 'Hello!'}
```

Lambdas and procs can be called in three different ways. Now if we fully golf everything by removing whitespace:

```ruby
a=Proc.new{|s|print s}
b=lambda{|s|print s}
c=->s{print s}

a['Hello!']; a.('Hello!'); a.call('Hello!') #=> Hello!Hello!Hello!
b['Hello!']; b.('Hello!'); b.call('Hello!') #=> Hello!Hello!Hello!
c['Hello!']; c.('Hello!'); c.call('Hello!') #=> Hello!Hello!Hello!
```

The `[]` notation is a bit ambiguous since arrays use the same operator to access elements, but disambiguity is not a goal in code-golf! Use `[]` because it costs one less byte than `.()`.

This example demonstrates how storing blocks in stabby lambdas can save a few bytes:

```ruby
# 34 bytes
a=->x{print b=x*3,$/,x+' '+x,$/,b}

# 41 bytes
def b x
  print a=x*3,$/,x+' '+x,$/,a
end

# Lambda call
a['@']

# @@@
# @ @
# @@@

# Method call
b'@'

# @@@
# @ @
# @@@
```

However, do note that actually calling the lambda does add two more bytes thanks to having to call with `[]`.

## Shortening maps by using `&` and `to_proc` <a name="shortening_maps_by_using_&_and_to_proc"></a>

The typical *Ruby* way of iterating through arrays is to use the `each` method:

```ruby
[1,2,3,4].each{|x| print x}
# 1234
```

It is important to note that this does not return any object.

However, an alternative is the `map` method, which is one byte shorter. Although this also depends on the situation, because `map` does return an object:

```ruby
[1,2,3,4].map{|x| print x}
# 1234
#=> [nil,nil,nil,nil]
```

In this case, an array of four `nil` objects, since the block doesn't actually call any methods on the actual object. However, if we call methods on the object, it does return a new array:

```ruby
['this','is','code','golf'].map{|x| x.reverse}
#=> ['siht','si','edoc','flog']
```

Consider the following examples:

```ruby
[[2],[2,8],[5,7,4],[]].map{|x| x.length}
#=> [1, 2, 3, 0]

['this','is','code','golf'].map{|x| x.reverse}
#=> ['siht','si','edoc','flog']

[1,2,3,4,5].map{|x| x**3}
#=> [1, 8, 27, 64, 125]

['hello',' ','world!'].each{|x| print x}
# hello world!

[1,4,9,16].map{|x| Math.sqrt x}
#=> [1.0, 2.0, 3.0, 4.0]

['1.txt','2.txt','3.txt'].map{|f| File.read f}
#=> ['...', '...', '...']
```

All of these examples either iterate through the array and call a method on each individual element, e.g. `x.length`, `x.reverse`, or call a method with the element as a parameter e.g. `print x`, `Math.sqrt x` and `File.read f`.

Most of the time, the map can be shortened by using the `&` operator. If you are calling a method on the elements of the array, it's straightforward:

```ruby
# 39 bytes
[[2],[2,8],[5,7,4],[]].map{|x|x.length}
# 36 bytes
[[2],[2,8],[5,7,4],[]].map(&:length)
# 35 bytes - you can remove parentheses but need the space!
[[2],[2,8],[5,7,4],[]].map &:length
```

```ruby
# 45 bytes
['this','is','code','golf'].map{|x|x.reverse}
# 41 bytes
['this','is','code','golf'].map &:reverse
```

All that `&` is doing in this case is calling the `to_proc` method on the given symbol, allowing it to be passed as a stored block into the `map` method, which is expecting a block.

The examples below use the same idea, but a bit differently since we're calling methods with the element as a parameter.

As you can see though, in a lot of these kinds of cases, it's almost always better to just stick with the literal `{}` block format, due to `.method` costing quite a few bytes, having to use parentheses, and having to specify the class or object from which the method is in:

```ruby
# 39 bytes
['hello',' ','world!'].each{|x|print x}
# 50 bytes - you need the parentheses this time!
['hello',' ','world!'].each &Kernel.method(:print)
```

```ruby
# 30 bytes
[1,4,9,16].map{|x|Math.sqrt x}
# 34 bytes
[1,4,9,16].map &Math.method(:sqrt)
```

```ruby
# 45 bytes
['1.txt', '2.txt', '3.txt'].map{|f|File.read f}
# 49 bytes
['1.txt', '2.txt', '3.txt'].map &File.method(:read)
```

```ruby
# 25 bytes
[1,2,3,4,5].map{|x| x**3}
# 30 bytes
[1,2,3,4,5].map &3.method(:**)
```

As seen with the last example, you should almost always never use `&` and `to_proc` if you are just using Ruby operators like `<<`, `+`, `-`, `**`, etc.

The only situation where this may be useful is when you'll have to use the same method repeatedly. Just for sake of demonstration, let's pretend you can't do use fractional powers to find roots, and we want to get the 64th root. Here we can use the fact that a variable can be assigned with a `Method` type:

```ruby
# 85 bytes
[1, 2**64, 3**64].map{|x|Math.sqrt Math.sqrt Math.sqrt Math.sqrt Math.sqrt Math.sqrt x}
#=> [1.0, 2.0, 3.0]

# 81 bytes
s=Math.method:sqrt
[1, 2**64, 3**64].map(&s).map(&s).map(&s).map(&s).map(&s).map &s
#=> [1.0, 2.0, 3.0]
```

But as you can see, these situations are extremely rare, and in this case only 4 bytes were saved.

---

|      Previous article       |              Next article               |
| :-------------------------: | :-------------------------------------: |
| [General](../articles/1.md) | [IO, Kernel and ARGV](../articles/3.md) |

