# Strings, symbols and regular expressions

*Quick tips such as interpolation to cut down on bytes wherever strings are involved, and understanding search patterns and the `Regexp` class.*

------

- [String interpolation over concatenation](#string_interpolation_over_concatenation)
- [Interpolate with instance variables](#interpolate_with_instance_variables)
- [Use `%` notation](#use_percentage_notation)
- [Use `?` for ASCII characters](#use_?_for_ascii_characters)
- [Use `chars` instead of `split`](#use_chars_instead_of_split)
- [Split strings without using a parameter](#split_strings_without_using_a_parameter)
- [Use symbols when appropriate](#use_symbols_when_appropriate)
- [Check inclusion without the regex match operator](#check_inclusion_without_the_regex_match_operator)

---

## String interpolation over concatenation <a name="string_interpolation_over_concatenation"></a>

The general rule of thumb is that string interpolation saves more bytes than concatenation. Especially when you would have to call object conversion methods such as `to_s` on the objects:

```ruby
# concat.rb - 67 bytes
a=1
b=2
puts'The value of '+a.to_s+'+'+b.to_s+' is '+(a+b).to_s+'.'
# The value of 1+2 is 3.
```

```ruby
# inter.rb - 47 bytes
a=1
b=2
puts"The value of #{a}+#{b} is #{a+b}."
# The value of 1+2 is 3.
```

However for simple strings, especially those where you don't have to call object conversion methods, it may be better to use concatenation:

```ruby
# concat.rb - 22 bytes
x='Ruby!'
puts x+' '+x
# Ruby! Ruby!
```

```ruby
# inter.rb - 25 bytes
x='Ruby!'
puts"#{x} #{x}"
# Ruby! Ruby!
```

## Interpolate with instance variables <a name="interpolate_with_instance_variables"></a>

When interpolating a single variable `var` into a string, we normally have to do `"#{var}"`. However, if we declare the variable as an instance variable `@var` we actually don't need the `{}`, so `"#@var"`, saving two bytes. You can also do this with global variables like `$var`.

Though if interpolating an expression, it is better to interpolate the whole expression rather than assigning a new instance variable, as seen below:

```ruby
# var.rb - 78 bytes
a=1
b=2
c=3
puts"The value of #{a}+#{b}-#{c} is #{a+b+c}."
# The value of 1+2-3 is 0.
```

```ruby
# inst.rb - 65 bytes
@a=1
@b=2
@c=3
@d=@a+@b-@c
puts"The value of #@a+#@b-#@c is #@d."
# The value of 1+2-3 is 0.
```

```ruby
# inst2.rb - 61 bytes
@a=1
@b=2
@c=3
puts"The value of #@a+#@b-#@c is #{@a+@b+@c}".
# The value of 1+2-3 is 0.
```

## Use `%` notation <a name="use_percentage_notation"></a>

Ruby has a useful way to quote strings, inspired by Perl. This is done by using `%` followed by a non-alphanumeric delimiter such as `{}`, `[]`, `()`, `<>`, or even `??` or `--`. But if using any of the bracket delimiters, then those same delimiters can appear *unescaped* in the string as long as they are in *balanced* pairs:

```ruby
# 68 bytes - must use backslashes to escape quotes
p "\"Shoo!\" said Mr. Dursley (loudly). \"No!\" said Harry, boldly."
# "\"Shoo!\" said Mr. Dursley (loudly). \"No!\" said Harry, boldly."

# 65 bytes
p %{"Shoo!" said Mr. Dursley {loudly}. "No!" said Harry, boldly.}
# "\"Shoo!\" said Mr. Dursley {loudly}. \"No!\" said Harry, boldly."

# 65 bytes
p %<"Shoo!" said Mr. Dursley {loudly}. "No!" said Harry, boldly.>
# "\"Shoo!\" said Mr. Dursley {loudly}. \"No!\" said Harry, boldly."
```

But any brackets within the string that match the delimiter, must be in matching pairs:

```ruby
p %<"Shoo!" said Mr. Dursley <loudly><.>
#=> unterminated string meets end of file (SyntaxError)
```

## Use `?` for ASCII characters <a name="use_?_for_ascii_characters"></a>

For single ASCII characters (`0-127`), we can use the precede the character by `?` to declare a string, rather than surrounding the character by `''`, saving one byte:

```ruby
'a'.class #=> String
?a.class #=> String
```

```ruby
# 26 bytes
('a'..'z').map{|x|print x}
# abcdefghijklmnopqrstuvwxyz

# 24 bytes
(?a..?z).map{|x|print x}
# abcdefghijklmnopqrstuvwxyz
```

## Use `chars` instead of `split` <a name="use_chars_instead_of_split"></a>

If you need to get the characters in the string as an array, using `chars` instead of `split''` saves two bytes:

```ruby
# 24 bytes
print'code-golf'.split''
# ["c", "o", "d", "e", "-", "g", "o", "l", "f"]

# 22 bytes
print'code-golf'.chars
# ["c", "o", "d", "e", "-", "g", "o", "l", "f"]
```

## Split strings without using a parameter <a name="split_strings_without_using_a_parameter"></a>

The default parameter of the `String#split` method is whitespace `' '`, so when splitting strings by spaces, don't use a parameter and you'll save three bytes:

```ruby
# 36 bytes
'this is code-golf in ruby'.split' '
#=> ["this", "is", "code-golf", "in", "ruby"]

# 33 bytes
'this is code-golf in ruby'.split
#=> ["this", "is", "code-golf", "in", "ruby"]
```

## Use symbols when appropriate <a name="use_symbols_when_appropriate"></a>

A very basic and obvious tip, but strings are surrounded by two ASCII characters, `''` or `""`. However, symbols are only preceded by a single ASCII character `:`, saving one byte. 

However, this is only really desirable if using `puts` or `print` for output, since `p` will print out the preceding `:` for the symbol, and the surrounding `""` for the string:

```ruby
puts :a,'a'
# a
# a

print :a, 'a'
# aa

p :a, 'a'
# :a
# "a"
```

 ## Optimize length conditionals <a name="optimize_length_conditionals"></a>

Though this concerns strings, it also applies to arrays. Suppose we want to print `"that's a long string"` if a given string is longer than 8 characters, or `"that's a short string"` otherwise. For a string `s`, we can do `s[8]` instead of `s.length>8` to check whether the length is greater than 8, because if the string length is less than 8 characters long, `s[8]` evaluates to `nil`, which is a falsey value:

```ruby
A='code-golf'
B='codegolf'
```

```ruby
# 56 bytes
f=->x{puts"that's a #{x.length>8?:long: :short} string"}

f[A]
# that's a long string
f[B]
# that's a short string
```

```ruby
# 50 bytes
g=->x{puts"that's a #{x[8]?:long: :short} string"}

g[A]
# that's a long string
g[B]
# that's a short string
```

## Check inclusion without the regex match operator <a name="check_inclusion_without_the_regex_match_operator"></a>

To find out whether one string contains a single character, avoid using regex and the match operator `=~`, and instead just use `[]` on the string, along with `?` notation for ASCII characters:

```ruby
# 43 bytes
puts'this is code-golf!'[?-]?:∃golf: :∄golf
# ∃golf

# 43 bytes
puts'this is code-golf!'[?z]?:∃golf: :∄golf
# ∄golf
```

```ruby
# 44 bytes
puts'this is code-golf!'=~/-/?:∃golf: :∄golf
# ∃golf

# 44 bytes
puts'this is code-golf!'=~/z/?:∃golf: :∄golf
# ∄golf
```

------

|             Previous article             |               Next article               |
| :--------------------------------------: | :--------------------------------------: |
| [IO, Kernel and ARGV](../articles/IO,%20Kernel%20and%20ARGV.md) | [Math and numerics](../articles/Math%20and%20numerics.md) |

