---
layout: post
title: "Hooked on Ruby"
description: ""
category: 
tags: []
---
{% include JB/setup %}

{% highlight ruby %}
#
# Go here for the runnable version: https://bit.run/s/5ts-In6lwLgFveVW```
#
#  Out of the box Ruby hooks/callbacks
#
#  Module#prepended
#  Module#extended
#  Module#included
#  Module#method_added
#  Module#method_removed
#  Module#method_undefined
#  Module#const_missing
#  
#  BasicObject#initialize
#  BasicObject#method_missing
#  BasicObject#singleton_method_added
#  BasicObject#singleton_method_removed
#  BasicObject#singleton_method_undefined
#  
#  Kernel#respond_to_missing?
#  
#  Class#inherited
#  
#  Object#initialize_clone
#  Object#initialize_dup
#  Object#initialize_copy

puts <<EOS


# # # # # # # # # # # # # #
# Module Hooks / Callbacks
# # # # # # # # # # # # # #
EOS
module Papa;
  def papa_method
  end
end

module Foo
  def self.prepended(klass)
    puts "\nFoo was prepended to #{klass}"
  end

  def self.included(klass)
    puts "\nFoo was included to #{klass}"
  end

  def self.extended(klass)
    puts "\nFoo was extended to #{klass}"
  end

  def self.method_added(method_name)
    puts "\nAdded '#{method_name}' method on #{self}"
  end

  def self.method_removed(method_name)
    puts "\nRemoved '#{method_name}' method on #{self}"
  end

  def self.method_undefined(method_name)
    puts "\nUndefined '#{method_name}' method on #{self}"
  end

  def self.const_missing(konst)
    puts "\nWhat happened to '#{konst}' on #{self}?"
  end
end

class Bar; prepend Foo; end
class Baz; include Foo; end
class Quux; extend Foo; end
Foo::Offnoober

module Foo 
  include Papa
  def did_i_win_powerball?; false; end
  remove_method :did_i_win_powerball?
  undef_method :papa_method
end

puts <<EOS


# # # # # # # # # # # # # # # #
# BasicObject Hooks / Callbacks
# # # # # # # # # # # # # # # #
EOS

class Bar
  def initialize(*args)
    puts "\nInitialized called with arguments: #{args.inspect}" unless args.include?(:ssh)
  end

  def method_missing(method, *args, &block)
    puts "\nSent '#{method}' with #{args.inspect} #{'and a block' if block}".strip
  end

  def singleton_method_added(method_name)
    puts "\nAdded '#{method_name}' method to module #{self}"
  end

  def singleton_method_removed(method_name)
    puts "\nremoved '#{method_name}' method to module #{self}"
  end

  def singleton_method_undefined(method_name)
    puts "\nundefined '#{method_name}' method to module #{self}"
  end
end
Bar.new
Bar.new(:foo, 1, { baz: "QUUX" })
puts
Bar.new(:shh).my_method(1,2,3)
Bar.new(:ssh).my_method(1,2,3) { |x| x * 2}

obj = Bar.new :ssh
def obj.my_singleton;end

obj.singleton_class.send :remove_method, :my_singleton
# or shovel in:  class << obj; remove_method :my_singleton; end 

puts <<EOS


# # # # # # # # # # # # # #
# Kernel Hooks / Callbacks
# # # # # # # # # # # # # #
EOS

class Respond
  def foo
  end

  def method_missing(m, *args, &block)
    if m == :my_method
      puts "Yes I respond to '#{m}'"
    else
      super
    end
  end

  def respond_to_missing?(method_name, include_private = false)
    # Bonus, check the singleton_class?
    method_name == :my_method ||  # NOTE: Comment this line to see the default behavior
    super
  end
end

r = Respond.new
puts "r.respond_to?(:foo): #{r.respond_to?(:foo)}"
puts "r.respond_to?(:bar): #{r.respond_to?(:bar)}"
puts "r.respond_to?(:my_method): #{r.respond_to?(:my_method)}"
puts r.my_method

puts <<EOS


# # # # # # # # # # # # # #
# Class Hooks / Callbacks
# # # # # # # # # # # # # #
EOS
class Mama
  def self.inherited(klass)
    puts "\n#{self} was inherited by #{klass}"
  end
end

class Child < Mama
end
puts <<EOS


# # # # # # # # # # # # # #
# Object Hooks / Callbacks
# # # # # # # # # # # # # #
EOS

class TheRealMcCoy
  def initialize_clone(obj)
    puts "initialize_clone passed #{obj} by #{self}"
    super
  end

  def initialize_copy(obj)
    puts "initialize_copy passed #{obj} by #{self}"
  end

  def initialize_dup(obj)
    puts "initialize_dup passed #{obj} by #{self}"
    super
  end
end

trm = TheRealMcCoy.new
trm.dup
puts
trm.clone
trm_copy = trm
{% endhighlight %}
