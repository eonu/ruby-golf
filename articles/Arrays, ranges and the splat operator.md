# Arrays, ranges and the splat operator

*Techniques for array and range manipulation and optimization, and understanding the Ruby splat operator.*

------

- [Use `%` for declaring arrays of strings or symbols](#use_percentage_for_declaring_arrays_of_strings_or_symbols)
- [Use `Array#*` instead of `Array#join`](#use_array#*_instead_of_array#join)
- [Use the exclusive range operator `...`](#use_the_exclusive_range_operator_...)
- [Use `Array#<<` instead of `Array#push`](#use_array#<<_instead_of_array#push)
- [Don't use `Array#uniq` to eliminate duplicates](#don't_use_array#uniq_to_eliminate_duplicates)
- [Don't use `Range#member?` or `Range#include?` to check for range membership](#don't_use_range#member_or_range#include_to_check_for_range_membership)
- [Use `Я` and `ᴙ` for ranges of 2 and 3 letters](#use_Я_and_ᴙ_for_ranges_of_2_and_3_letters)
- [Use the splat operator `*` instead of `to_a`](#use_the_splat_operator_*_instead_of_to_a)
- [Use `Integer#downto` for decreasing integer ranges](#use_integer#downto_for_decreasing_integer_ranges)
- [Use `[]` instead of `Array#new` for array initialization](#use_[]_instead_of_array#new_for_array_initialization)
- [Use the splat operator `*` to declare singleton arrays](#use_the_splat_operator_*_to_declare_singleton_arrays)
- [Eliminate `[]` for array assignment](#eliminate_[]_for_array_assignment)
- [Array destructuring and pattern matching with the splat operator `*`](#array_destructuring_and_pattern_matching_with_the_splat_operator_*)
- [Use the splat operator `*` to flatten arrays](#use_the_splat_operator_*_to_flatten_arrays)

---

## Use `%` for declaring arrays of strings or symbols <a name="use_percentage_for_declaring_arrays_of_strings_or_symbols"></a>

Rather than declaring arrays of strings with the typical `[]`, we can use `%w()` syntax, which eliminates the need for any quotation marks around the elements in the array. Similarly, `%i()` can be used for symbols, eliminating the need for any preceding `:` before each symbol element:

```ruby
# 41 bytes
p ['this','is','code','golf','in','ruby']
#=> ["this", "is", "code", "golf", "in", "ruby"]

# 31 bytes
p %w(this is code golf in ruby)
#=> ["this", "is", "code", "golf", "in", "ruby"]
```

```ruby
# 35 bytes
p [:this,:is,:code,:golf,:in,:ruby]
#=> [:this, :is, :code, :golf, :in, :ruby]

# 31 bytes
p %i(this is code golf in ruby)
#=> [:this, :is, :code, :golf, :in, :ruby]
```

Although it looks ugly, you can actually still use spaces with this format if you escape them with a `\`:

```ruby
# 35 bytes
p ['this is','code golf','in ruby']
#=> ["this is", "code golf", "in ruby"]

# 34 bytes
p %w(this\ is code\ golf in\ ruby)
#=> ["this is", "code golf", "in ruby"]
```

This saves even more bytes when it's an array of symbols with spaces, since you'd otherwise need a preceding `:` as well as surrounding quotation marks around the symbol text:

```ruby
# 38 bytes
p [:'this is',:'code golf',:'in ruby']
#=> [:"this is", :"code golf", :"in ruby"]

# 34 bytes
p %i(this\ is code\ golf in\ ruby)
#=> [:"this is", :"code golf", :"in ruby"]
```

## Use `Array#*` instead of `Array#join` <a name="use_array#*_instead_of_array#join"></a>

When you need to join an array of strings or symbols with a delimiter, you can use the `*` method instead of `join`, which saves 3 bytes.

Note that with the `*` method, if your delimiter is a single ASCII character, you can `?` notation for the delimiter, further saving another byte. You can't do this with `join` however:

```ruby
# 39 bytes
p %w(this is code golf in ruby).join'-'
#=> "this-is-code-golf-in-ruby"

# 38 bytes - doesn't work
p %w(this is code golf in ruby).join?-
#=> syntax error, unexpected end-of-input (SyntaxError)

# 35 bytes - better
p %w(this is code golf in ruby)*'-'
#=> "this-is-code-golf-in-ruby"

# 34 bytes - best!
p %w(this is code golf in ruby)*?-
#=> "this-is-code-golf-in-ruby"
```

You might find joining with the new-line operator `$/` instead of `'\n'` quite useful too:

```ruby
# 29 bytes
print %w(ruby code golf)*'\n'
# ruby
# code
# golf

# 27 bytes
print %w(ruby code golf)*$/
# ruby
# code
# golf
```

## Use the exclusive range operator `...` <a name="use_the_exclusive_range_operator_..."></a>

Sometimes it may save a byte if you use the exclusive range operator:

```ruby
n=7

# 25 bytes
(0..n-1).each{|x|print x} #=> 0123456

# 24 bytes
(0...n).each{|x|print x} #=> 0123456
```

## Use `Array#<<` instead of `Array#push` <a name="use_array#<<_instead_of_array#push"></a>

Note that the `<<` method is also present in the `String` class, and can be used for the same purpose.

Note that you don't need a space after `<<` in this example too, since the precedence of `+` is higher than that of `<<`:

```ruby
a=[1,1,2,3,5,8]

# 31 bytes
5.times{a.push a[-1]+a[-2]};p a
#=> [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]

# 27 bytes
5.times{a<<a[-1]+a[-2]};p a
#=> [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
```

## Don't use `Array#uniq` to eliminate duplicates <a name="don't_use_array#uniq_to_eliminate_duplicates"></a>

It saves bytes to simply take the union `|` with an empty array:

```ruby
# 32 bytes
p [1,0,0,1,1,0,2,0,2,2,1,3].uniq #=> [1, 0, 2, 3]

# 30 bytes
p [1,0,0,1,1,0,2,0,2,2,1,3]|[] #=> [1, 0, 2, 3]
```

## Don't use `Range#member?` or `Range#include?` to check for range membership <a name="don't_use_range#member_or_range#include_to_check_for_range_membership"></a>

If you want to find out if an element is contained within a given range, you can use the `Range#===` method:

```ruby
# 24 bytes
p (?a..?z).include?'﷽'
#=> false

# 23 bytes
p (?a..?z).member?'﷽'
#=> false

# 16 bytes
p (?a..?z)==='﷽'
#=> false
```

## Use `Я` and `ᴙ` for ranges of 2 and 3 letters <a name="use_Я_and_ᴙ_for_ranges_of_2_and_3_letters"></a> 

Pretty random characters, right? Remember that these characters are not ASCII, so they don't save as many bytes as you'd think, but they are still useful for that `-1` byte:

```ruby
# 17 bytes
p (?a..'zz').to_a
#=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z", "aa", "ab", "ac", "ad", "ae", ..., "zw", "zx", "zy", "zz"]

# 16 bytes
p (?a..?Я).to_a
#=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z", "aa", "ab", "ac", "ad", "ae", ..., "zw", "zx", "zy", "zz"]
```

```ruby
# 18 bytes
p (?a..'zzz').to_a
#=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z", "aa", "ab", "ac", "ad", "ae", ..., "zw", "zx", "zy", "zz", "aaa", "aab", ..., "zzw", "zzx", "zzy", "zzz"]

# 17 bytes
p (?a..?ᴙ).to_a
#=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z", "aa", "ab", "ac", "ad", "ae", ..., "zw", "zx", "zy", "zz", "aaa", "aab", ..., "zzw", "zzx", "zzy", "zzz"]
```

## Use the splat operator `*` instead of `to_a` <a name="use_the_splat_operator_*_instead_of_to_a"></a>

[There's alot to say about the splat operator](https://endofline.wordpress.com/2011/01/21/the-strange-ruby-splat/) especially when dealing with method parameters, but it is especially useful for converting things to arrays.

For ranges, simply precede the range by the splat operator. Alot of the time you don't even need to use parentheses, just square brackets:

```ruby
# 21 bytes
p (?a..?z).to_a.class #=> Array

# 19 bytes
p [*(?a..?z)].class #=> Array

# 17 bytes
p [*?a..?z].class #=> Array
```

## Use `Integer#downto` for decreasing integer ranges <a name="use_integer#downto_for_decreasing_integer_ranges"></a>

Sadly Ruby doesn't allow you to do backwards ranges like `(10..1)`. However, instead of converting the ascending range into an array and reversing it, you can use the `downto` method.

Note that the parentheses for the `downto` method are required in this case:

```ruby
# 18 bytes
p [*1..10].reverse #=> [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]

# 17 bytes
p [*10.downto(1)] #=> [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

However, `downto` is only a method in the `Integer` class, so this doesn't work for strings:

```ruby
# 19 bytes
p [*?a..?h].reverse #=> ["h", "g", "f", "e", "d", "c", "b", "a"]

# 18 bytes - doesn't work
p [*?h.downto(?a)] #=> undefined method `downto' for "h":String (NoMethodError)
```

## Use `[]` instead of `Array#new` for array initialization <a name="use_[]_instead_of_array#new_for_array_initialization"></a>

Simple, looks better and saves 7 bytes:

```ruby
# 11 bytes
a=Array.new

# 4 bytes
a=[]
```

## Use the splat operator `*` to declare singleton arrays <a name="use_the_splat_operator_*_to_declare_singleton_arrays"></a>

A quick way to save one byte:

```ruby
# 9 bytes
a=[:ruby]
p a #= [:ruby]

# 8 bytes
*b=:ruby
p b #=> [:ruby]
```

## Eliminate `[]` for array assignment <a name="eliminate_[]_for_array_assignment"></a>

Another quick one, and this one saves two bytes:

```ruby
# 25 bytes
a=[1,2,3,4]
p a.class #=> Array
p a #=> [1, 2, 3, 4]

# 23 bytes
b=1,2,3,4
p b.class #=> Array
p b #=> [1, 2, 3, 4]
```

## Array destructuring and pattern matching with the splat operator `*` <a name="array_destructuring_and_pattern_matching_with_the_splat_operator_*"></a>

This is more like general Ruby advice, but the splat operator is very powerful when it comes to pattern matching. It's quite remniscient of the Haskell pattern match operator `:`.

First, a few things that can be done without the splat operator:

- Multiple assignment

  ```ruby
  c=%i(hello world)
  a,b=c[0],c[1]
  p a,b
  # :hello
  # :world
  ```

- Multiple assigment with an array

  ```ruby
  a,b=%i(hello world)
  p a,b
  # :hello
  # :world
  ```

And now, a few array destructuring tricks that can be done with the splat operator:

- Assigning a variable to the first element of an array:

  ```ruby
  first, *rest = [1, 2, 3, 4, 5]
  p first #=> 1
  p rest #=> [2, 3, 4, 5]
  ```

  You don't even have to give the *rest* of the array a variable name if you don't need to:

  ```ruby
  first, * = [1, 2, 3, 4, 5]
  p first #=> 1
  ```

  In fact, you don't even need the splat operator at all! But you still need the comma:

  ```ruby
  first, = [1, 2, 3, 4, 5]
  p first #=> 1
  ```

- Assigning a variable to the last element of the array:

  ```ruby
  *rest, last = [1, 2, 3, 4, 5]
  p rest #=> [1, 2, 3, 4]
  p last #=> 5
  ```

  And again, you don't have to give the *rest* of the array a variable name if you don't need to:

  ```ruby
  *, last = [1, 2, 3, 4, 5]
  p last #=> 5
  ```

  However, this time we can't eliminate the splat operator:

  ```ruby
  , last = [1, 2, 3, 4, 5]
  p last #=> unexpected ',', expecting end-of-input (SyntaxError)
  ```

- Assigning a variable to the elements in the middle of the array, or variables to both the first and last elements of the array:

  ```ruby
  first, *rest, last = [1,2,3,4,5]
  p first #=> 1
  p rest #=> [2, 3, 4]
  p last #=> 5
  ```

  And again, you don't have to give the elements in the middle of the array a variable name if you don't need to:

  ```ruby
  first, *, last = [1,2,3,4,5]
  p first #=> 1
  p last #=> 5
  ```

## Use the splat operator `*` to flatten arrays <a name="use_the_splat_operator_*_to_flatten_arrays"></a>

Though this only really applies to nested arrays, it can save quite a few bytes over `flatten`, especially over smaller arrays where you wouldn't have to use many splat operators:

```ruby
# 45 bytes
p [[:a], :b, [:c, :d], :e, %i(f g h)].flatten
#=> [:a, :b, :c, :d, :e, :f, :g, :h]

# 41 bytes
p [*[:a], *:b, *[:c, :d], :e, *%i(f g h)]
#=> [:a, :b, :c, :d, :e, :f, :g, :h]
```

------

|             Previous article             |
| :--------------------------------------: |
| [Math and numerics](../articles/Math%20and%20numerics.md) |

