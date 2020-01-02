---
layout: post
title:  "Ruby-Metaprogramming"
date:   2018-01-19 10:26:24
categories: Ruby
---

### Module and Class
```ruby
Wa = Module.new
Wa.class # Module
Wa.superclass # Throw Exception 'NoMethodError' here. 
 # Means 'modules' do not have parents or son. 'modules' are alone. 
 # Only can be included or include other 'modules'. 
Wa.new # Throw Exception 'NoMethodError' here. Means 'modules' couldn't be initialized.
Wa

Wa.module_eval { def a; puts 'a'; end }
Wa.class_eval { def b; puts 'b';end }

Ca = Class.new
Ca.module_eval { include Wa }
Ca.new.a # a
Ca.new.b # b
 # 'module_eval' is equal to 'class_eval'. 
 # They can be used on 'modules' or 'classes'.
 # They both means 'Open Class'.
```

```ruby
Wa = Module.new {
  def a; end
  def b; end
}

Ca = Class.new

Mo = Module.new {
  include Wa # 'include' method is defined at 'Module' class, 
             # so a module can include another module.

  def c; end
}

Ca.class_eval do
  include Mo
end

puts Ca.instance_methods.include?(:a) && Ca.instance_methods.include?(:b) # true

puts Mo.instance_methods.include?(:a) && Mo.instance_methods.include?(:b) # true
  # 'instance_methods' is defined at 'Module', so a 'module' also have use it.
  # So most methods used for 'class' can also be used for 'module'. 

puts Ca.instance_methods.include?(:c) # true
  
```

```ruby
module Mo
  def kk
    @f = 5 # This 'instance_variable' is used for a initialized Class. 
           # Module could not be initialized. But we can define 'instance_variable' here.
  end
end

class Abc
  include Mo
end

# Abc.include(Mo) # This line is equal to the previous three lines.

a = Abc.new
a.kk # If not executed, the 'instance_variable' @f is nil.
puts a.instance_variable_get(:@f).inspect # 5
puts Abc.instance_methods.grep(/k/).inspect # [:kk, :kind_of?]
puts Abc.instance_methods == a.methods # true
puts Class.instance_methods == Abc.methods # true

inherited = false
Class.instance_methods(inherited) # [:allocate, :new, :superclass]

class Bc < Abc
  define_method(:abc) do
    # Here we entered the scope of instance! The same as in method :abcd .
  end
  
  def abcd
    # Here we entered the scope of instance! The same as in method :abc .
  end
end

puts Bc.superclass == Abc # true

```

```ruby
# In top-level env, 'self' is object 'main'. 
self.respond_to?(:define_method, true) 
# Return true. But 'define_method' method is defined at 'Module' class, 
# so only modules have method 'define_method'. 'main' is a special object. Matz just want to make programming easy, so he make 'main' different,
# then you can use the defined method after the method defined in main. 
```

```ruby
class GGG
end

ggg = GGG.new

ggg.instance_eval do
  def kkkkgg
    puts "kkkk"
  end
end

ggg.kkkkgg # kkkk. So 'instance_eval' can enter object's 'eigenclass' scope. 
 # This is different form 'class_eval'. 'class_eval' is defined in 'Module', but
 # 'instance_eval' is defined in 'Object'. 
 # 'class_eval' means open class, the method defined in 'class_eval' is used for instance. 
```

```ruby
# These below looks like 'Classes', because they are upper cased first letter. 
# But actually they are methods. They are defined in 'Kernel' module, so they can be used by any objects.
# puts self.private_methods # Then you can see them.
String(5)
Array("some")
Integer("5")
```

```ruby
module Rack
  # Here the code in file 'rack/builder' is not executed until you use the class 'Rack::Builder'.
  autoload :Builder, "rack/builder"
  # But 'require' is different from 'autoload' because it will run the code of "something" immediately.
  # So 'rack' use 'autoload' to announce load all the 'modules' it needs but not actually load they. 
  require "something"
end
```

`obj.singleton_class` is `obj's eigenclass`. Let's prove it.
```ruby
class Tom
  puts "class Tom's singleton_class is #{self.singleton_class.inspect}"

  class << self
    puts "class Tom's eigenclass is #{self.inspect}"
    puts "#{self == Tom.singleton_class}" # true
  end
end

tom = Tom.new

tom_sc = tom.singleton_class

puts "#{tom_sc.inspect} #{tom_sc.object_id}" #<Class:#<Tom:0x00007fe88f14bae8>>70318404820300

tom_sc.instance_eval do
  puts "#{self.inspect} #{self.object_id}" #<Class:#<Tom:0x00007fe88f14bae8>>70318404820300

  puts "#{tom.singleton_class == self}" # true
end
```

When I was reading Rails source code in `railties/lib/rails/application.rb`, I see this code
```ruby
def instance
  super.run_load_hooks!
end
```
What does this mean? It really confused me.
After a deep research, I realize that this code is equal to
```ruby
def instance
  a_returned_value = super # Keyword 'super' will call the parent's same name method. In this example, it is method named 'instance'.
  a_returned_value.run_load_hooks!
end
```
Many people may think `super.run_load_hooks!` means `self.superclass.run_load_hooks!` That's wrong.

* Prefer lambda to proc because lambda's `return` and passing `parameters` are good for more using condition.

```ruby
class MyClass
  def initialize(value)
    @x = value
  end
  
  def my_method
    @x  
  end
end

object = MyClass.new(2)
m = object.method(:my_method)
m.call
```

```ruby
class MyClass
  class << self
    puts self.object_id # 70137814629400
  end
end

sc = MyClass.singleton_class
puts sc.object_id # 70137814629400

MyClass.class_eval do
  # Will treat MyClass as a class. So current class is 'MyClass'.

  def abc
    puts "-- abc"
  end

  puts "#{self == MyClass}" # true
end

MyClass.instance_eval do
  # Will treat MyClass as an object. So current class is 'sc'.
  # Will define a method in class 'sc'.
  def i_t
    puts "-- i_t"
  end

  puts "#{self == MyClass}" # true
end

mc = MyClass.new
mc.abc
# mc.i_t # Will throw exception because 'i_t' is a method for MyClass. 'i_t' is defined in MyClass's singleton_class
MyClass.i_t
```

* Do you know why the methods defined under the top level context `self is 'main'` can be used for all objects?

Because in the top level context, the current class is `main`'s class: `Object`. That mean you're defining methods in class Object.





