# Math and numerics

*Deciding which type of numeric to use, how to declare them, and general advice for any math involved in golfing.*

------

- [Use the bitwise complement `~` to increment and decrement](#use_the_bitwise_complement_~_to_increment_and_decrement)
- [Declare large or small floats with standard form](#declare_large_or_small_floats_with_standard_form)
- [Don't use `<=` or `>=` for comparisons](#don't_use_leq_or_geq_for_comparisons)
- [Use the `Integer#times` method for looping](#use_the_integer#times_method_for_looping)
- [Use `Kernel#p` to output numerics](#use_kernel#p_to_output_numerics)
- [Use literals for `Complex` and `Rational` initialization](#use_literals_for_complex_and_rational_initialization)

---

## Use the bitwise complement `~` to increment and decrement <a name="use_the_bitwise_complement_~_to_increment_and_decrement"></a>

The bitwise complement operator `~` is a unary operator. It essentially flips the bits in a binary number. Remember that negative binary numbers are stored as the two's complement of the positive counterpart, so the bitwise complement will also change the sign.

This can be useful for the incrementation and decrementation of integers and operating on the new number, since you can eliminate the parentheses:

```ruby
# 16 bytes
x=7
puts (x+1)*2
# 16

# 14 bytes
y=7
puts -~y*2
# 16
```

But note that since the precedence of the exponentiation operator `**` is greater than that of the bitwise complement, we can't use this trick. The calculation being performed is `-((~y)**2)`, which is incorrect, since we want `(-~y)**2`:

```ruby
x=7
puts (x+1)**2
# 64

y=7
puts -~y**2
# -64
```

However, you can use it in a similar way to decrement:

```ruby
# 16 bytes
x=7
puts (x-1)*2
# 12

# 14 bytes
y=7
puts ~-y*2
# 12
```

## Declare large or small floats with standard form <a name="declare_large_or_small_floats_with_standard_form"></a>

A very useful trick when dealing with powers of `10` less than `-2` or greater than `0`, as floats. This can be great for dealing with percentages or unit prefixes like giga, micro, nano etc:

```ruby
p 0.0001==1e-4 #=> true
p 0.001==1e-3 #=> true
p 10.0==1e1 #=> true
p 100.0==1e2 #=> true
p 1000.0==1e3 #=> true

p 1e5.class #=> float
```

## Don't use `<=` or `>=` for comparisons <a name="don't_use_leq_or_geq_for_comparisons"></a>

Very straightforward, just use `<` and `>` instead (obviously after changing your conditions), and you save one byte.

## Use the `Integer#times` method for looping <a name="use_the_integer#times_method_for_looping"></a>

Not using `for` or `while` loops should really be a rule for Ruby in general.

If you want to execute a selected block of code repeatedly, for a fixed number of times, you should use the `Integer#times` method:

```ruby
# 25 bytes - looks ugly and spans multiple lines
for i in 0..7
	print i
end
# 01234567

# 38 bytes - can't use {} for the block and spans multiple lines
i=0
while i<8 do 
  print i
  i+=1
end
# 01234567

# 19 bytes - perfect!
8.times{|i|print i}
# 01234567
```

Though, when using `puts`,  `p` or `print`, there are sometimes better ways to do it:

```ruby
# 25 bytes
3.times{print'code-golf'}
# code-golfcode-golfcode-golf

# 18 bytes
print'code-golf'*3
# code-golfcode-golfcode-golf
```

```ruby
# 19 bytes
5.times{puts :ruby}

# 14 bytes
puts [:ruby]*5
```

## Use `Kernel#p` to output numerics <a name="use_kernel#p_to_output_numerics"></a>

`Kernel#p` was annoying for outputting strings because it would also print out the (sometimes unwanted) `""`. It was the same story with symbols and printing out the preceding `:`.

However, `Kernel#p` is a bit nicer with numerics:

```ruby
p Math::PI #=> 3.141592653589793
p 10000.0 #=> 10000.0
p 1e4 # 10000.0
p 50 #=> 50
p 7 #=> 7
```

## Use literals for `Complex` and `Rational` initialization<a name="use_literals_for_complex_and_rational_initialization"></a>

Not many people know that the `Rational` and `Complex` classes have literals which you can use for initialization:

```ruby
complex = [5+2.i, Complex(5,2)]

puts complex.map{|c|{class: c.class, conjugate: c.conj}}
# {:class=>Complex, :conjugate=>(5-2i)}
# {:class=>Complex, :conjugate=>(5-2i)}

# 15 bytes
p Complex(5,-2) #=> (5-2i)

# 7 bytes
p 5-2.i #=> (5-2i)
```

```ruby
rational = [4.7r, Rational(47,10), Rational(141,30)]

puts rational.map{|r|{class: r.class, frac: r, dec: r.to_f}}
#{:class=>Rational, :frac=>(47/10), :dec=>4.7}
#{:class=>Rational, :frac=>(47/10), :dec=>4.7}
#{:class=>Rational, :frac=>(47/10), :dec=>4.7}

# 17 bytes
p Rational(47,10) #=> (47/10)

# 6 bytes
p 4.7r #=> (47/10)
```

------

|             Previous article             |               Next article               |
| :--------------------------------------: | :--------------------------------------: |
| [Strings, symbols and regular expressions](../articles/4.md) | [Arrays, ranges and the splat operator](../articles/6.md) |

