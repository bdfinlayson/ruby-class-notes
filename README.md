# ruby-class-notes
My class notes from the Ruby/Rails semester at Nashville Software School

2015-4-10
----------
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
