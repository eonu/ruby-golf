# IO, Kernel and ARGV

*How to handle input and output for the given code-golf problem.*

------

- [Eliminate lambdas (and methods)](#eliminate_lambdas_(and_methods))
- [ARGV](#argv)
- [IO](#io)
- [Kernel](#kernel)
- [Kernel - `print`, `puts` and `p`](#kernel_-_print,_puts_and_p)
- [Kernel - `gets` and `chomp`](#kernel_-_gets_and_chomp)
- [Kernel - global variables](#kernel_-_global_variables)

---

## Eliminate lambdas (and methods) <a name="eliminate_lambdas_(and_methods)"></a>

You might be thinking that this conflicts with what has been said recently, but using `IO`, `ARGV` and `Kernel` to handle input and output of your code-golf solutions is almost always more efficient. This is because you can use certain methods from `IO` and `ARGV` to read input from the command line. And any input given to the command line doesn't actually count towards the bytes in your code golf solution. The program below can be executed by running `$ ruby a.rb` in the command line:

```ruby
# a.rb - 36 bytes
a=->x,y{puts x+y}
a['hello','world']

# helloworld
```

However, the program below can take arguments via command line. You simply list the arguments after the Ruby file like `$ ruby b.rb hello world `:

```ruby
# b.rb

# 20 bytes
puts ARGV[0]+ARGV[1]
# helloworld

# 14 bytes
puts ARGV.join
# helloworld

# 12 bytes
puts ARGV*''
# helloworld
```

By simply using command line arguments, we saved ourselves quite a few bytes, and there are even more tricks we can use to save bytes.

## ARGV <a name="argv"></a>

As you could probably tell from the example above, `ARGV` is simply just an array of the given command line arguments:

```ruby
ARGV.class #=> Array
```

And as such, you can access the arguments just like you would with a normal array, and you can use any method from the `Array` class on `ARGV`:

```ruby
# file.rb
puts ARGV.length
p ARGV.reverse
```

```bash
$ ruby file.rb
0
[]

$ ruby file.rb hello world
2
["world","hello"]
```

However, note that all command line arguments are read as strings, so don't try using different types:

```ruby
# classes.rb
p ARGV.map &:class
```

```bash
$ ruby golf.rb "say" :hello 2 world
[String, String, String, String]
```

## IO <a name="io"></a>

> The [IO](https://ruby-doc.org/core-2.3.1/IO.html) class is the basis for all input and output in Ruby.

There's a lot of information to know about `IO`, but [this blog post](https://robots.thoughtbot.com/io-in-ruby) explains it brilliantly.

## Kernel <a name="kernel"></a>

Though `IO` also deals with input and output, it's often the methods from the `Kernel` module that are used, because they are normally just short-hand calls of methods from the `IO` class.

For example (as explained in the blog post above), every Ruby programmer has probably used `puts` to print output with a newline after it. `puts` is actually a method from the `Kernel` module, i.e. `Kernel#puts`, and this method is essentially just the short-hand version of `$stdout.puts`, where `$stdout` is simply a global variable that references the `STDOUT` constant in the `IO` class.

## Kernel - `print`, `puts` and `p` <a name="kernel_-_print,_puts_and_p"></a>

`Kernel#print`, `Kernel#puts` and `Kernel#p` are all methods used for output.

A few global variables (denoted by `$`) are used in the explanations and examples below. These will be explained later.

- `print` - Outputs each argument and separates them with the output field separator `$,` (defaults to nil), and doesn't print a new line at the end. Objects that aren’t strings will be converted by calling their `to_s` method. If no arguments are given, prints `$_`. Returns `nil`.

  ```ruby
  print 'this', 'is', 'print'
  # 'thisisprint'
  #=> nil
  ```

  ```ruby
  $, = '#'
  print 'this', 'is', 'print'
  # 'this#is#print
  #=> nil
  ```

- `puts` - Outputs each argument and separates them with the newline operator `'\n'` or `$/`, and prints a new line at the end. If called with an array argument, writes each element on a new line. Returns `nil`.

- `p` - `p obj` is equivalent to `puts obj.inspect`, but returns the object if it was `nil` or a single argument, so more like `puts obj.inspect; obj`. If multiple arguments were given, `puts arg.inspect` is called on each argument (`arg`), then an array of all of the arguments is returned:

  ```ruby
  p
  #=> nil

  p 'hello'
  # "hello"
  #=> "hello"

  p 'hello', :world
  # "hello"
  # :world
  #=> ["hello", :world]
  ```

  `p` is actually very useful in a number of ways:

  - Using `p` without arguments can save 2 bytes if you need `nil`

    ```ruby
    # 1 byte
    p
    #=> nil

    # 3 bytes
    nil
    #=> nil
    ```

  - Similarly, it is the shortest way to get `true`:

    ```ruby
    # 2 bytes
    !p
    #=> true

    # 4 bytes
    true
    #=> true
    ```

  - In some cases, you can use `p` instead of `puts` to save 3 bytes, or `print` to save 4 bytes. However, note that using `p` with strings will print out surrounding `"` marks around the string, and also the preceding `:` for symbols, which might not be desirable. But it can be useful for arrays:

    ```ruby
    p 'hello'
    # "hello"
    puts 'hello'
    # hello
    print 'hello'
    # hello

    p :hello
    # :hello
    puts :hello
    # hello
    print :hello
    # hello

    p [1,'hello',:world]
    # [1, "hello", :world]
    print [1,'hello',:world]
    # [1, "hello", :world]
    ```

## Kernel - `gets` and `chomp` <a name="kernel_-_gets_and_chomp"></a>

- `gets` is another useful method for getting input. When `gets` is called in the command line, Ruby waits for input from the user. Once enter is pressed, `gets` reads the input given by the user, and adds the newline `'\n'` (or `$/`) from the `Enter` press at the end:

  ```ruby
  # input.rb
  p gets
  ```

  ```bash
  $ ruby input.rb
  hello world
  "hello world\n"
  ```

  If using `gets`, another useful thing you can do is to use `~/$/` to get the length of the input. It's even more useful because it ignores trailing newlines:

  ```ruby
  # input.rb

  # 6 bytes
  i=gets

  # 13 + 6 bytes - counts the trailing newline
  puts i.length

  # 19 + 6 bytes
  puts i.chomp.length

  # 8 bytes + 6 bytes - don't need the space!
  puts~/$/
  ```

  ```bash
  $ ruby input.rb
  this is code-golf
  18
  17
  17
  ```

- `chomp` is simply a method in the `String` class that gets rid of trailing newlines:

  ```ruby
  # input.rb
  i=gets
  p i,i.chomp
  ```

  ```bash
  $ ruby input.rb
  hello world
  "hello world\n"
  "hello world"
  ```

## Kernel - global variables <a name="kernel_-_global_variables"></a>

As seen previously, the `Kernel` module offers a number of special global variables which could be considered one of the most useful Ruby tools for code-golf, because the variable names are very short.

These global variables can all be accessed with the `Kernel#global_variables` method, which is simply an array of symbols representing each global variable:

```ruby
global_variables
#=> [:$;, :$-F, :$@, :$!, :$SAFE, :$~, :$&, :$`, :$', :$+, :$=, :$KCODE, :$-K, :$,, :$/, :$-0, :$\, :$_, :$stdin, :$stdout, :$stderr, :$>, :$<, :$., :$FILENAME, :$-i, :$*, :$:, :$-I, :$LOAD_PATH, :$", :$LOADED_FEATURES, :$?, :$$, :$VERBOSE, :$-v, :$-w, :$-W, :$DEBUG, :$-d, :$0, :$PROGRAM_NAME, :$-p, :$-l, :$-a, :$binding, :$1, :$2, :$3, :$4, :$5, :$6, :$7, :$8, :$9]
```

This list doesn't tell us much about what they mean though. Here are a few that are useful for code-golf. Especially useful global variables are shown in **bold**. More can be found [here](https://medium.com/ruby-golf/ruby-golf-cheatsheet-eb27ec2cdf60):

| Global variable | Description                              |
| --------------- | ---------------------------------------- |
| `$~`            | The `MatchData` instance of the last match. |
| `$&`            | Depends on `$~`. The string matched by the last successful match. |
| ``$` ``         | Depends on `$~`. The string to the left of the last successful match. |
| `$'`            | Depends on `$~`. The string to the right of the last succesful match. |
| `$+`            | Depends on `$~`. The highest group matched by the last successful match. |
| `$1`            | Depends on `$~`. The Nth group of the last successful match. |
| **`$/`**        | **The input record separator. Defaults to newline `\n`.** |
| **`$\`**        | **The output record separator. Defaults to `nil`.** |
| **`$,`**        | **The output field separator for `print` and `Array#join`. Defaults to nil.** |
| **`$;`**        | **The default separator for `String#split`.** |
| `$.`            | The current line number of the last file from input. |
| **`$<`**        | **Same as `ARGF`.**                      |
| **`$>`**        | **The default output for `print`, `printf`. Defaults to `$stdout`.** |
| **`$_`**        | **The last input line of string by `gets` or `readline`.** |
| `$0`            | Contains the name of the script being executed. May be assignable. |
| **`$*`**        | **Same as `ARGV`.**                      |
| `$$`            | The process number of the Ruby running this script. Read only. |
| `$?`            | The status of the last executed child process. Read only. |
| `$:`            | Same as `$LOAD_PATH`.                    |
| `$"`            | The array contains the module names loaded by require. |

Examples of situations where these global variables would be useful:

- This challenge was to print out a given string in a square. Using the lambda approach, you can see that we have to call `x.length`:

  ```ruby
  # 83 bytes
  a=->x{l=(x.length**0.5).ceil;l.times{|i|l.times{|j|print x[i*l+j]};puts if i!=l-1}}

  # Called with 14 bytes
  a['parameter']

  # par
  # ame
  # ter
  ```

  Since we have to make use of the length of the string, why not make use of `gets` and `~/$/`?

  ```ruby
  # golf.rb - 79 bytes (and no bytes required for calling)
  x=gets;l=(~/$/**0.5).ceil;l.times{|i|l.times{|j|print x[i*l+j]};puts if i!=l-1}
  ```

  Called from the command line:

  ```bash
  $ ruby golf.rb
  parameter
  par
  ame
  ter
  ```

- Using `$*` instead of `ARGV`:

  ```ruby
  # lambda.rb - 36 bytes
  a=->x,y{p x+y}
  a['hello','world']
  # "helloworld"
  #=> "helloworld"
  ```

  Running `$ ruby args.rb hello world` in command line (remember that since `$*` is a global variable, we don't need the space after the `p` method call):

  ```ruby
  # args.rb

  # 11 bytes
  p ARGV.join
  # "helloworld"
  #=> "helloworld"

  # 8 bytes
  p$*.join
  # "helloworld"
  #=> "helloworld"
  ```

  Saves a massive 28 bytes!

- This challenge was to make a box with the given character, `ignoring trailing newlines`. This is a solid attempt using a lambda, and the `$/` newline operator:

  ```ruby
  # 41 bytes
  a=->x{print y=x*3,$/,x+' '+x,$/,y};a['O']

  # OOO
  # O O
  # OOO
  ```

  A similar approach can be made, but instead by assigning `$,` to `$/`. Remember that `$,` specifies what separator the `print` method uses, so if we set it to the newline operator `$/`, we can print each parameter on a new line. In this example, it doesn't save any bytes, but if you had more parameters for the `print` method, it would. This is just another example to demonstrate how to use `Kernel` global variables:

  ```ruby
  # 41 bytes
  $,=$/;b=->x{print y=x*3,x+' '+x,y};b['O']

  # OOO
  # O O
  # OOO
  ```

  Another straightforward approach would be to just use the `puts` method, since it automatically separates parameters by a new line, and we don't care about the trailing new line in this challenge:

  ```ruby
  # 34 bytes
  c=->x{puts y=x*3,x+' '+x,y};c['O']

  # OOO
  # O O
  # OOO
  ```

  This approach uses `$*` and command line arguments, but with the `*` method on the array to specify `$/` as the separator between each element. Again, this doesn't save any more bytes than the previous example, but demonstrates `Kernel`'s global variables:

  ```ruby
  # 34 bytes
  d=$*[0];print [y=d*3,d+' '+d,y]*$/

  # OOO
  # O O
  # OOO
  ```

- Using `$><<` instead of `puts`.

  Note that this doesn't save any bytes when using `print` for `String` or `Symbol`. But `$><<` returns an `STDOUT` object, which may or may not be useful:

  ```ruby
  # 10 bytes
  print:hello
  # hello

  # 10 bytes
  $><<:hello
  # hello
  #=> #<IO:<STDOUT>>
  ```

  But remember the problem we had with eliminating the space after `print` for numeric types like `print1.4`, `print3`, or even arrays like `print['hi']` and `print%w(hello world).length`? Well `$><<` can handle that removed space just fine, saving two bytes each time:

  ```ruby
  $><<1.4
  # 1.4
  #=> #<IO:<STDOUT>>

  $><<3
  # 3
  #=> #<IO:<STDOUT>>

  $><<['hi']
  # ["hi"]
  #=> #<IO:<STDOUT>>

  $><<%w(hello world).length
  # 2
  #=> #<IO:<STDOUT>>
  ```

------

|             Previous article             |               Next article               |
| :--------------------------------------: | :--------------------------------------: |
| [Methods, blocks and lambdas](../articles/Methods,%20blocks%20and%20lambdas.md) | [Strings, symbols and regular expressions](../articles/Strings,%20symbols%20and%20regular%20expressions.md) |

