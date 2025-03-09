---
layout: post
title:  "Study from Ruby official website"
date:   2024-10-11 11:34:24
categories: Ruby
---

## Ruby APIs
### Array
* `slice` is equal to `[]`. It support range as a parameter, like `[1,3,5,7,9].slice(1..3)` will return `[3,4,7]` (right inclusive).
* `slice!` it accept `integer` or `range` as parameter, and delete the items covered for the original array.
```ruby
arr = [1,3,5,7,9]
arr.slice!(1..3) # return [3,4,7]
arr # is [1,9]
```

### Hash
https://ruby-doc.org/3.3.5/Hash.html

```ruby
h = Hash.new(-1) # with default value -1
h.default # => -1
h[:xxx] # => -1

x = 'abc'
y = 'ddd'
h = {x:, y} 
h[:x] # return 'abc', since last line, the value is omitted (deduct with the variable name).

# https://ruby-doc.org/3.3.5/Hash.html#method-i-slice
h = {foo: 0, bar: 1, baz: 2}
h.slice(:baz, :foo) # => {:baz=>2, :foo=>0}

h = {foo: 0, bar: 1, baz: 2}
h1 = h.to_h {|key, value| [value, key] }
h1 # => {0=>:foo, 1=>:bar, 2=>:baz}

h = {foo: 0, bar: 1, baz: 2}
h.delete_if {|key, value| value > 0 } # => {:foo=>0}

# https://ruby-doc.org/3.3.5/Hash.html#method-i-default_proc
synonyms = Hash.new { |hash, key| hash[key] = [] } 
synonyms.include?(:hello) # => false
synonyms[:hello] << :hi # => [:hi]
synonyms[:world] << :universe # => [:universe]
synonyms.keys # => [:hello, :world]
```

* https://ruby-doc.org/3.3.5/Struct.html
* Class Struct provides a convenient way to create a simple class that can store and fetch values.

```ruby
class Abc
end

a = Abc.new
b = Abc.new
puts a.class === b # true
puts b.class === a # true
puts a === a.class # false
puts b === b.class # false
puts a === b # false
puts a == b # false
puts "abc" === 'abc' # true
puts a.class === b.class # false
```
* `===` is a method, which could be defined in different classes. We need to read the relevant API document of the class. 
