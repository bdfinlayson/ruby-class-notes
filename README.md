# ruby-class-notes
My class notes from the Ruby/Rails semester at Nashville Software School

2015-4-10 - Present
----------
If vs Unless statements
----------------
Anytime you see `if !something` you can replace that code with `unless`:

    if !@current_item.nil?
      #some code
    end

Can be refactored to: 

    unless @current_item.nil?
      #some code
    end

#### Namespacing
In the [about_constants koan](https://github.com/bdfinlayson/ruby-koans/blob/8ec8bf61f5f348fd1f926b1afaffe6f466e7e2d4/about_constants.rb), in the example beginning with `class MyAnimals::Oyster < Animal`, the class `Oyster` has 4 legs because it is not inside of the box named MyAnimals, so when we inherit to `Oyster` we are getting `Animal` and not getting `MyAnimals`.

#### Loops
`while` loops are typically used for data streams, where you don't know how much data you're going to get and you want to run your program while you still have data coming in.

#### Exceptions
`fail` and `raise` are interchangeable
`raise` statements make good guard statements at the top of methods. For example:
    raise TriangleError unless values[0] + values[1] > values[2]

#### attr_accessor and attr_reader
`attr_accessor` is used to create set and get methods in classes. For
example, this:

    class Tweet
      attr_accessor :status
      def initialize(status)
        @status = status
      end
    end

is the same as this:

    class Tweet
      def initialize(status)
        @status = status
      end
      def status=(status)
        @status = status
      end
      def status
        @status
      end
    end

If we don't want to allow a variable to be set outside of the class,
you can use the `attr_reader` method, which creates a get but not a
set method. 

Setting Default Values
----------------------
Ruby versions 1.9 and above do not require that you set default values for parameters. However, it still may be useful to set default values to make your code more concise. In the example below, the method returns a default value of a string if it is passed no parameters:

    def say_hello(name = "My name is Bryan")
      "Hello, my name is #{name}."
    end

Using default values also allows you to call a method without passing parameters to it, whereas if you wrote your method like this:

    def say_hello(name)
      #some code
    end

The method above would throw an ArgumentError if you called it without passing any arguments to it.
      

Print vs Puts
-------------
The difference between `print` and `puts` is that `print` doesn't put a new line (i.e. `\n`) at the end.

Using the Splat * Operator with `inject`
-------------------------
The splat operator is used to handle methods which have a variable parameter list (i.e. can take any number of parameters). In the example below, `inject` is used to iterate over the arguments.

    def add(*numbers)
      numbers.inject { |accumulator, number| accumulator + number }
    end

The `inject` method also accepts an argument that is "injected" into the accumulator when the method is done iterating through all the arguments:

    def add(*numbers)
      numbers.inject(10) { |accumulator, number| accumulator + number }
    end
    
    add(10)
    
    #=> 20

You can also use `inject` to manipulate strings. If you use `inject` with strings, the `inject` method will insert the injected value to the beginning of the string:

    def my_string(*strings)
      strings.inject("Because he is a good coder, ") { |accumulator, string| accumulator.concat(string) }
    end
    
    my_string("Bryan ", "is ", "the ", "man.")
    
    #=> "Because he is a good coder, Bryan is the man."
    
You can also use the splat operator when calling methods. For example, you can feed an array with three elements to a method taking three arguments, and the splat operator will interpret each element in the array as a separate argument:

    def nums(one_num, two_num, three_num)
      one_num + two_num + three_num
    end
    
    num_array = [1, 2, 3]
    
    puts nums(*num_array)
    
    #=> 6
    
If you pass an array like this with too many elements, the method will throw an ArgumentError. However, you can use the splat operator to "vaccuum" up all arguments after it, as shown in the example below:

    def introduction(age, gender, *names)
      name = names.to_a.join(" ")
      "Meet #{name}, who's #{age} and #{gender}."
    end
    
    introduction(30, "Male", "Bryan", "David", "Finlayson")
    
    #=> "Meet Bryan David Finlayson, who's 30 and Male."

Options as a Parameter
----------------------
You can pass configuration options to a method as key/value pairs:

    def add(a_number, another_number, options = {})
      sum = a_number + another_number
      sum = sum.abs if options[:absolute]
      sum = sum.round(options[:precision]) if options[:round]
      sum
    end
    
    puts add(1.0134, -5.568)
    puts add(1.0134, -5.568, absolute: true)
    puts add(1.0134, -5.568, absolute: true, round: true, precision: 2)
    
    #=> -4.5546
    #=> 4.5546
    #=> 4.55
    
Ruby makes this possible by allowing the last parameter in the parameter list to skip using curly braces if it's a hash, making for a much prettier method invocation. That's why we default the `options` to `{}` - because if it isn't passed, it should be an empty `Hash`.

As a consequence, the first invocation in the example has two parameters, the second, three and the last, seemingly five. In reality, the second and third invocations both have three parameters - two numbers and a hash.

Lambdas
---------
A lambda is essentially an object of the class Proc. The last expression of a lambda is its return value, just like regular functions. In addition, lambdas have methods that can be assigned to variables. And anytime you use a lambda, you must assign it a block of code, as in:

    l = lambda { 1 + 2 }
    l.call
    
    #=> 3
    
The lambda `{}` can also accept block arguments. This lambda increments any number passed to it by 1:

    Increment = lambda { |x| x + 1 } 
    Increment.call(1)
    
    #=> 2



Lambdas also take parameters by surrounding their code block with a `do..end`, as in this example:

    l = lambda do |string|
      if string == "Bryan"
        return "Not him!" 
      else
        return "Okay, as long as you're not Bryan, you may pass!"
      end
    end
    puts l.call("Sam")
    
    #=> Okay, as long as you're not Bryan, you may pass!
    
The convention followed in Ruby is to use `{}` for single line lambdas and `do..end` for lambdas that are longer than a single line.

Lambdas vs Blocks
------------------
A lambda is a piece of code that you can store in a variable, and is an object. The simplest explanation for a block is that it is a piece of code that can't be stored in a variable and isn't an object. It is, as a consequence, significantly faster than a lambda, but not as versatile and also one of the rare instances where Ruby's "everything is an object" rule is broken.

Modules
-----------
Ruby modules allow you to create groups of methods that you can then include or mix into any number of classes. Modules only hold behaviour, unlike classes, which hold both behaviour and state.

Since a module cannot be instantiated, there is no way for its methods to be called directly. Instead, it should be included in another class, which makes its methods available for use in instances of that class. 

In order to include a module into a class, we use the method `include` which takes one parameter - the name of a `Module`. For example:

    module Greetings
      def generic_hello
        "What's up dude!"
      end
      def goodmorning
        "Top of the mornin' to ya!"
      end
      def goodafternoon
        "Fine day isn't it!"
      end
      def goodnight
        "Goodnight to you!"
      end
    end
    
    class College_Student
      include Greetings
      
      def excuse
        "My dog ate my homework."
      end
    end
    
    class Regular_Fella
      include Greetings
      
      def about_work
        "On my way to the office now!"
      end
    end
    
    class Optimist
      include Greetings
      
      def positive_saying
        "Life is like a box of chocolates..."
      end
    end
      
    puts College_Student.new.generic_hello
    puts Regular_Fella.new.goodmorning
    puts Optimist.new.goodafternoon
    
    #=> What's up dude!
    #=> Top of the mornin' to ya!
    #=> Fine day isn't it!


Modules can be useful for making calculations:

    module Perimeter
      def perimeter
        sides.inject(0) { |sum, side| sum + side }
      end
    end
    
    class Rectangle
      include Perimeter
      
      def initialize(length, breadth)
        @length = length
        @breadth = breadth
      end
    
      def sides
        [@length, @breadth, @length, @breadth]
      end
    end
    
    class Square
      include Perimeter
      
      def initialize(side)
        @side = side
      end
    
      def sides
        [@side, @side, @side, @side]
      end
    end
    
    Rectangle.new(2,3).perimeter
    Rectangle.new(5,10).perimeter
    Square.new(5).perimeter
    Square.new(15).perimeter
    
    #=> 10
    #=> 30
    #=> 20
    #=> 60

Sidenote: Just like all classes are instances of Ruby's Class, all modules in Ruby are instances of Module. Interestingly, however, Module is the superclass of Class, so this means that all classes are also modules, and can be used as such.

This also means that modules can also hold classes. This allows classes or modules with conflicting names to co-exist while avoiding namespacing collisions. In the example below, there are two classes of Arrays alongside each other. This is possible because we've namespaced our version of the Array class under the Perimeter module:

    module Perimeter
      class Array
        def initialize
          @size = 400
        end
      end
    end
    
    our_array = Perimeter::Array.new
    ruby_array = Array.new
    
    p our_array.class
    p ruby_array.class
    
    #=> Perimeter::Array
    #=> Array

Note: the `::` is a constant lookup operator that looks up the `Array` constant only in the `Perimeter` module.

Ternary if Statements
-----------------
The basic structure of a ternary statement is `your condition ? your return if true : your return if false`

The `? :` structure basically represents `if..else`

    string = "Bryan"
    string.include?("B") ? "String found" : "String not found"
    
    #=> "String found"
    
You can also do nested ternary if statements:

    string = "Bryan"
    string.include?("x") ? "yes" : string.include?("b") ? "yes it does" : "no it doesn't"
    
    #=> "yes it does"
    
Enumerable
------------
Enumerable includes methods that iterate through a data structure, such as an Array. One example of 

    arr = [1,2,3]
    
    arr.map { |x| x * 2 if x == 1 }
    
     #=> [2, nil, nil]
     
Use `compact` to remove all instances of `nil` from the returned array:
    
    arr.map { |x| x * 2 if x == 1 }.compact
    
     #=> [2]
     
Ternary if statements can also be used with `map`:
    
    arr.map { |x| x == 2 ? x * 2 : x }
    
     #=> [1, 4, 3]
     
`map!` alters the original array:
    
    arr.map! { |x| !(x == 2) ? x * 2 : x }
    
     #=> [2, 2, 6]

Double Bang
-------------
Use a a `!!` to coerce a boolean value from something that wouldn't normally return a boolean:

    "Bryan".match(/Bryan/)
    
     #=> #<MatchData "Bryan">
     
    !!"Bryan".match(/Bryan/)
     
     #=> true
    
ActiveRecord & SQL
--------------
Essentially connects your Ruby code (i.e. a Ruby object) to a database. More specifically, an ActiveRecord object. If you want to communicate with your database through your model you have to do it through the ActiveRecord class. Schema is the thing or blueprint that describes your data model. ActiveRecord is a domain specific language for creating databases that map into SQL. Once stored in a SQL database, we can write SQL querries to get the data from the database. ActiveRecord in addition to pushing and pulling data also gives us a language to play around with. Arel is a library that sits behind ActiveRecord ([for more see this]( http://jpospisil.com/2014/06/16/the-definitive-guide-to-arel-the-sql-manager-for-ruby.html)). SQL is just a language to talk to a relational database. A foreign key is a key in a table that points to another table. This is what a relational database is about. Each table contains data that points to another table. ([For more, see this]( http://blog.codinghorror.com/a-visual-explanation-of-sql-joins/)). 

Big O Notation
----------
Talks about the spacial or computational complexity of an algorithem. Every computation can be representated by O(n), where n is the number elements that you are dealing with. Ruby uses a bubble sort algorithem for its `sort` method. The more complex your computations, the more inefficient is your program.

This is how you would might sort in Ruby:
    users.sort_by {|x| x.created_at }

This is how you would do the same thing in ActiveRecord:
    User.order('created_at desc')
    
Anytime you see `desc` it means you are asking for the values in decending order.

Declarative vs Imperative
--------------------------

Symbol To Proc
--------------
`&:attributes` is an example of symbol to Proc. The following code is an example of symbol to Proc:

    User.select('first)name').map &:attributes 
    
which is Ruby short hand for:

    User.select(first_name).map { |x| x.attributes }

PostGres
-------
PostGres is a relational database engine. MySQL is similar but inferior to PostGres.

RegEx
------
`i`
The `i` in `/john/i` is for case-insensitive regex.

Lazy Evaluation & Design Patterns
----------------------
`compose / composition` = using small pieces to build up something larger

`decompose` = extract smaller bits from a larger body of code

`lazy evaluation` = an example of lazy evaluation is storing a method like a SQL call in a variable and calling it only when you need it

#####Decorator Pattern
What a decorator is, say you have a user and you want to show a link to their homepage, so you create a decorator that wraps the user inside of it. An example of a decorator is below:

    class UserDecorator
      def initialize(user)
        @user = user
      end
      def full_name
        "#{@user.first_name} #{@user.last_name}"
        .strip
      end
    end

####Adaptor Pattern (great for rubyists)

####Reactor Pattern

The dynamic nature of Ruby makes some design patterns irrelevant

What's important about the design patterns is using them as a form of expression when talking about them with other developers. They can help you solve problems but in the end not any single pattern can solve all problems (don't code yourself into a corner where you are using a single pattern). Multiple patterns are best in practice. Just keep in mind what patterns might be available.

File Modes
---------
In regards to the File class, `mode` is a string that specifies the way you would like a file to be opened. In the example below, `r+` opens a file in read-write mode, starting from the beginning: 

    mode = "r+"
    file = File.open("friend-list.txt", mode)
    puts file.inspect
    puts file.read
    file.close
    
    #=> #<FakeFS::File:0x00000002da6ea0>
        Jasim
        Neha
        Timothy
        Smit
        Nid
        Srihari
        Jithu
        Asif


If the `mode` is given as a String, it must be one of the values listed in [the following table from ruby-doc.org](http://ruby-doc.org/core-1.9.3/IO.html):

    Mode |  Meaning
    -----+--------------------------------------------------------
    "r"  |  Read-only, starts at beginning of file  (default mode).
    -----+--------------------------------------------------------
    "r+" |  Read-write, starts at beginning of file.
    -----+--------------------------------------------------------
    "w"  |  Write-only, truncates existing file
         |  to zero length or creates a new file for writing.
    -----+--------------------------------------------------------
    "w+" |  Read-write, truncates existing file to zero length
         |  or creates a new file for reading and writing.
    -----+--------------------------------------------------------
    "a"  |  Write-only, starts at end of file if file exists,
         |  otherwise creates a new file for writing.
    -----+--------------------------------------------------------
    "a+" |  Read-write, starts at end of file if file exists,
         |  otherwise creates a new file for reading and
         |  writing.
    -----+--------------------------------------------------------
     "b" |  Binary file mode (may appear with
         |  any of the key letters listed above).
         |  Suppresses EOL <-> CRLF conversion on Windows. And
         |  sets external encoding to ASCII-8BIT unless explicitly
         |  specified.
    -----+--------------------------------------------------------
     "t" |  Text file mode (may appear with
         |  any of the key letters listed above except "b").


Below is another example of how to open a file in Ruby. In the example, `what_am_i` writes to the file with `w` and returns `nil`. When `File.open` is called the second time to read the file using mode `r`, the string that was written to the file previously is returned:

    what_am_i = File.open("clean-slate.txt", "w") do |file|
      file.puts "Bryan is the man."
    end
    
    p what_am_i
    
    File.open("clean-slate.txt", "r") {|file| puts file.read }
    
    #=> nil
    #=> Bryan is the man.
    
This is another typical example of how to write to a file. This example uses the `File#write` method:

    File.open("disguise", "w") do |f|
      f.write "Bryan is the man."
    end
