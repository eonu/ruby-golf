# General

*General tips and advice for code-golf, including common (**non-language specific**) code-golf techniques.*

---

- [Eliminate whitespace](#eliminate_whitespace)
- [Eliminate parentheses](#eliminate_parentheses)
- [Shorten variable names](#shorten_variable_names)
- [Better control flow, conditionals and method returns](#better_control_flow,_conditionals_and_method_returns)
- [Operator precedence](#operator_precedence)
- [Use `;` to write code on one line](#use_;_to_write_code_on_one_line)

---

## Eliminate whitespace <a name="eliminate_whitespace"></a>

Remember that whitespace is just another ASCII character, and therefore takes up a byte. In most cases, Ruby allows you to eliminate whitespace without any negative consequences:

```ruby
C = 5

# From 22 bytes
puts 'Hello!' if C > 4

# To 18 bytes
puts'Hello!'if C>4
```

However, this doesn't work with variables, constants and methods followed or preceded by reserved Ruby words like `if`, or other method calls. For example, we can do `puts C` but not `putsC`.

What if we try to remove the space between `if` and `C`, or what if we replace `C` with an integer?

```ruby
puts'Hello!'ifC>4
#=> syntax error, unexpected tIDENTIFIER, expecting end-of-input puts'Hello!'ifC>4

puts'Hello!'if5>4
#=> syntax error, unexpected tIDENTIFIER, expecting end-of-input puts'Hello!'if5>4
```

However, it works with strings, symbols and arrays (and most of the other literals excluding the numbers, although this also varies):

```ruby
puts'Hello!'if[].length<4
# Hello!

puts'Hello!'if:s.class==Symbol
# Hello!

puts'Hello!'if'string'.bytesize>4
# Hello!
```

Another exception is with global variables:

```ruby
$a=5
puts'Hello!'if$a>4
puts$a

# Hello!
# 5
```

## Eliminate parentheses <a name="eliminate_parentheses"></a>

Just like whitespace, sometimes parentheses aren't needed in Ruby code.

```ruby
A = %w(this is an array of strings)

# From 11 bytes
A.drop(3) 
#=> ["array", "of", "strings"]

# To 10 bytes
A.drop 3 
#=> ["array", "of", "strings"]
```

But in some cases, you might need to keep parentheses for correct precedence or operation order (especially when dealing with arithmetic):

```ruby
# From 112 bytes
def distance(p1,p2)
  x1,y1=p1[0],p1[1]
  x2,y2=p2[0],p2[1]
  Math.sqrt((x2-x1)**2+(y2-y1)**2)
end

distance([1.0,1.0],[2.0,2.0])
#=> 1.4142135623730951
```

We can safely remove the parentheses in the method definition, method call and `Math.sqrt` call:

```ruby
# To 109 bytes
def distance p1,p2
  x1,y1=p1[0],p1[1]
  x2,y2=p2[0],p2[1]
  Math.sqrt (x2-x1)**2+(y2-y1)**2
end

distance [1.0,1.0],[2.0,2.0]
#=> 1.4142135623730951
```

Note the space after `Math.sqrt`. If it were not present, we would be doing `(Math.sqrt(x2-x1))**2 + (y2-y1)**2`, which is the wrong calculation.

But we also cannot remove the parentheses grouping `x2-x1` or `y2-y1`, as it changes the order of mathematical operations.

## Shorten variable names <a name="shorten_variable_names"></a>

Quite a simple one, but you'll always want to use single letter variable names. This example is quite a useless program, but it shows how many bytes it saves to use short variable names:

```ruby
# From 95 bytes
string='This is code-golf!'
string_length=string.length
string_length.times{|i|print string[i]}
# This is code-golf!

# To 56 bytes
s='This is code-golf!'
l=s.length
l.times{|i|print s[i]}
# This is code-golf!
```

Watch out though, some UTF-8 characters have more bytes than others, but can still be used as variable names:

```ruby
# 30 bytes
𠜎='𠜎'
puts 𠜎.bytesize
# 4

# 27 bytes
﷽='﷽'
puts ﷽.bytesize
# 3

# 21 bytes
a='a'
puts a.bytesize
# 1
```

Always use ASCII letters for your variable names!

## Better control flow, conditionals and method returns <a name="better_control_flow,_conditionals_and_method_returns"></a>

Ruby has a number of useful features for optimising your control flow and method returns - namely the ternary operator, postfix conditionals and automatic returning of the last evaluated expression

Consider the following four methods. They all produce the same output by printing `y` if the parameter is even or greater than 5, or `n` otherwise:

```ruby
def a(x)
  if x%2==0||x>5
    'y'
  else
    'n'
  end
end

def b(x)
  if x%2==0||x>5
    return'y'
  end
  'n'
end

def c(x)
  return'y'if x%2==0||x>5
  'n'
end

def d(x)
  x%2==0||x>5?'y':'n'
end
```

Testing with the integers from 1 to 10 proves that these methods are all equivalent:

```ruby
(1..10).map{|i|print a i} # nynynyyyyy
(1..10).map{|i|print b i} # nynynyyyyy
(1..10).map{|i|print c i} # nynynyyyyy
(1..10).map{|i|print d i} # nynynyyyyy
```

Looking at `a`, we can see that this is quite a straightforward method, but we don't have to use any `return` statements. This is because the last evaluated expression is either `y` or `n` depending on whether the condition is met or not, and Ruby always returns the last evaluated expression:

```ruby
# 58 bytes
def a(x)
  if x%2==0||x>5
    'y'
  else
    'n'
  end
end
```

However, looking at `b`, the explicit `return` statement is needed in this case because otherwise, the last evaluated expression would always be `'n'`, since it would always be executed after the `if` statement if we didn't use `return`:

```ruby
# 55 bytes
def b(x)
  if x%2==0||x>5
    return'y'
  end
  'n'
end
```

Another feature offered by the Ruby language is postfix conditionals - basically an `if` statement in one line. You can thank Perl for this! Note that the explicit `return` is needed again, otherwise `'n'` would always be the last evaluated expression:

```ruby
# 44 bytes
def c(x)
  return'y'if x%2==0||x>5
  'n'
end
```

The last implementation of this method utilises what is called the ternary operator, which is present in other programming languages too. One reason why ternary operators are often better than `if` or `unless` statements is that the entire statement evaluates to a single value, meaning you don't even have to use `return`:

```ruby
# 34 bytes
def d(x)
  x%2==0||x>5?'y':'n'
end
```

Here is an example of assignment with the ternary operator. You can do this:

```ruby
x=10

a=x%2==0||x>5?'y':'n'
```

But with `if` statements, you'd have to do something like this, since you can't assign the entire `if` statement to a variable:

```ruby
x=10

a=''
if x%2==0||x>5
  a='y'
else
  a='n'
end
```

Although it may be hard to understand what is happening, it is entirely possible to use nested ternary operators, and is almost always better than using nested `if` statements. Consider the following expression, which returns an age group of `'Adult'`, `'Teenager'` or `'Child'` depending on the value of the `age ` variable:

```ruby
age=19
puts (age >= 18) ? 'Adult' : (age < 13 ? 'Child' : 'Teenager')
```

Again, you can remove some parentheses and whitespace here and the program would still work fine:

```ruby
age=19
puts age>=18?'Adult':age<13?'Child':'Teenager'
```

Word of warning though, a lot of syntax highlighters will break when you mess around with parentheses, precedence and whitespace, especially if you use `Symbol` and `String` literals often, thanks to `:`, `''` and `""` causing a bit of confusion with the regular expressions used by the parsers when used without any surrounding whitespace. As long as the code does what you want, you can ignore the inconsistencies in syntax highlighting.

## Operator precedence <a name="operator_precedence"></a>

All operators in the Ruby language have their own precedence levels. These are important to consider, especially in code-golf, since parentheses are typically used to alter precedence levels, but we also want to keep the amount of parentheses to a minimum in code-golf. This table shows the precedence of all operators in order of highest to lowest:

| Method? | Operator                                 | Description                              |
| ------- | ---------------------------------------- | ---------------------------------------- |
| Yes     | `[]`, `[]=`                              | Element reference, element set           |
| Yes     | `**`                                     | Exponentiation (raise to the power)      |
| Yes     | `!`, `~`, `+`, `-`                       | Not, complement, unary plus and minus (method names for the last two are `+@` and `-@`) |
| Yes     | `*`, `/`, `%`                            | Multiply, divide and module              |
| Yes     | `+`, `-`                                 | Addition and subtraction                 |
| Yes     | `>>`, `<<`                               | Right and left bitwise shift             |
| Yes     | `&`                                      | Bitwise `AND`                            |
| Yes     | `^`, `\|`                                | Bitwise exclusive `OR` and regular `OR`  |
| Yes     | `<=`, `<`, `>`, `>=`                     | Comparison operators                     |
| Yes     | `<=>`, `==`, `===`, `!=`, `=~`, `!~`     | Equality and pattern match operators (`!=` and `!~` may not be defined as methods) |
| No      | `&&`                                     | Logical `AND`                            |
| No      | `\|\|`                                   | Logical `AND`                            |
| No      | `..`, `...`                              | Range (inclusive and exclusive)          |
| No      | `? :`                                    | Ternary operator (if-then-else)          |
| No      | `=`, `%=`, `{`, `/=`, `-=`, `+=`, `\|=`, `&=`, `>>=`, `<<=`, `*=`, `&&=`, `\|\|=`, `**=` | Assignment                               |
| No      | `defined?`                               | Check if specified symbol defined        |
| No      | `not`                                    | Logical negation                         |
| No      | `or`, `and`                              | Logical composition                      |
| No      | `if`, `unless`, `while`, `until`         | Expression modifiers                     |
| No      | `begin/end`                              | Block expression                         |

Any operator with `Yes` in the `Method?` column is a method, and can be redefined or called with the `.` operator. This may also lead to interesting and useful results:

```ruby
8/2+2 #=> 6
8/(2+2) #=> 2
8./(2+2) #=> 2
8./2+2 #=> 2
8./2.+2 #=> 2
```

If we want the expression to evaluate to `2`, `8./2+2` is one byte shorter than the solution `8/(2+2)`, since the explicit method call with the `.` operator doesn't need parentheses. 

Sometimes operator precedence can also make things work in unexpected ways. Consider:

```ruby
boolean_expression_1 = true && false
boolean_expression_2 = true and false
```

One would expect `boolean_expression_1` and `boolean_expression_2` to evaluate to the same boolean value of `false`, but that would be incorrect.

In `boolean_expression_1`, the `&&` operator has higher precedence than the assignment operator `=`, so `true && false` is evaluated first (evaluates to `false`), then that value is assigned to `boolean_expression_1`.

In `boolean_expression_2`, the assignment operator `=` has higher precedence than the logical `and` operator. This means that `boolean_expression_2` is assigned to the value `true` first, so we essentially have:

```ruby
(boolean_expression_2 = true) and false
```

And the value of this whole expression is `false`, but the actual value of the variable `boolean_expression_2` is `true`, which seems to conflict with the first example, where the variable evaluated to `false`, so watch out for your operator precedence.

Precedence allows you to have complex expressions without parentheses, as seen earlier with the ternary operator, but sometimes parentheses are needed.

I recommend first writing out your solution with parentheses and whitespace, then removing them and seeing if the code still works and produces the same output:

```ruby
# Start out with parentheses and whitespace
a = (((x%2)==0)||(x>5)) ? 'y' : 'n'
# Then eliminate them and see if the code still produces the same result
a=x%2==0||x>5?'y':'n'
```

## Use `;` to write code on one line <a name="use_;_to_write_code_on_one_line"></a>

Though this doesn't actually save any bytes since `;` takes up one byte just like the newline character `\n` does, it is often more desirable to write your code on one line:

```ruby
# 7 bytes
a=5
p a

# 7 bytes
a=5;p a
```

------

|               Next article               |
| :--------------------------------------: |
| [Methods, blocks and lambdas](../articles/Methods,%20blocks%20and%20lambdas.md) |