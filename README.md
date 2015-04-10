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
`raise TriangleError unless values[0] + values[1] > values[2]`

