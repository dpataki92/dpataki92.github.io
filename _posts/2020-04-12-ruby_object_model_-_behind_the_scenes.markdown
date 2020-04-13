---
layout: post
title:      "Ruby Object Model - Behind The Scenes"
date:       2020-04-13 02:18:20 +0000
permalink:  ruby_object_model_-_behind_the_scenes
---


The ruby object model is a complex topic. In order to really understand what is going on when we create those instance and class methods, it might worth zooming out of our usual instance-parent class relationship and taking a look at how exactly this object model works under the hood.

This may seem like a concept that we very rarely need to use directly, but I believe that it helps a lot in coming to terms with the techniques we use regularly and takes us one step closer to grasp something that we actually benefit from all the time, even without realizing it.

**The ancestor chain**

Okay, so the whole ruby object model is a chain of objects building upon each other in which the behavior of each object is defined by its ancestor classes. In this chain, almost every object inherits from another. The only exception is the last object, called the BasicObject.

![The Ruby Object Model ](https://4.bp.blogspot.com/-pidikfgz5gY/Vkuio4wYNFI/AAAAAAAAAX4/R63TVAWLhyw/s1600/1.png)

*(source: http://masala-blogg.blogspot.com/2015/11/unpacking-ruby-metaprogramming.html)*

If you want to know what classes and modules a particular object inherits from, you can easily check this by using the `#ancestors` method.

```
p [1,2,3].class.ancestors #=> [Array, Enumerable, Object, Kernel, BasicObject]
p 5.class.ancestors #=> [Integer, Numeric, Comparable, Object, Kernel, BasicObject]
p String.ancestors #=> [String, Comparable, Object, Kernel, BasicObject]
```

This method returns an array that shows the class hierarchy in order, containing the class and its included modules and then the parent class and its modules and so on. 

If you are only curious about the parent of a given class (superclass), you can also use the `#superclass` method:

```
p Object.superclass #=> BasicObject
p Array.superclass #=> Object
p Class.superclass #=> Module
```

**What do these objects do?**

*BasicObject* – The BasicObject is the parent class of all classes and the only one that does not inherit from another class. It contains the bare minimum of the methods required to create (e.g. `#new`) and compare (e.g. `#>=`, `#<=`) objects. It can be used to create your own alternative hierarchy separately from the original one by creating a subclass that inherits only from BasicObject.

```
class MyHierarchy < BasicObject
end
```

*Object* – The Object contains methods that are inherited by all classes, provided that the method is not explicitly overridden in the given class. Methods defined on Object are basic methods that make use of the receiver (e.g. `#instance_of ?`, `#freeze`, `#object_id`, etc.). Whenever a new program starts, Ruby creates the main object, which is an instance of the Object class. Therefore, main is the top-level context of all programs. You can easily test this by opening an empty ruby file and outputting self to the console.

`puts self #=> main`

*Kernel* – The Object class includes the Kernel module, so all its methods are available on all classes. Basically, the Kernel module includes core methods (e.g. `#puts` or `#raise`) that don't care about the receiver but act only on their arguments.

*Class* – Each class is an instance of the Class class. When you create a new object, the `#new` method in Class automatically runs and assigns the object to a global constant. Since each object is of type Class, this is also true for Object (or BasicObject). Class, however, is also a subclass of Object, which means that Object is a subclass of itself. Yes, weird.

**What can we learn from this?**

Well, first of all, when we say that we invoke a method on an object, we are not really exactly right. This is because objects do not have methods, only classes have.

| MyObject | MyClass | 
| -------- | -------- | -------- |
| class reference =>     | class reference =>      | 
| instance variables     | instance variables    | 
|          | methods table    |
|          | constants table    |
|          | superclass reference =>    |

Each method we invoke on an object is stored in the method table of its class. Each instance refers to this method table in order to access the methods. The class also refers to another class in the same way. If the class does not inherit from another class, it inherits from the Object.

```
daniel = MyClass.new
bonnie = MyClass.new
daniel.occupation = "Software engineer"
bonnie.occupation = "Lawyer"
p daniel.occupation #=> "Software engineer"
p bonnie.occupation #=> "Lawyer"
```

However, we know that we can also create a custom method on a given object.

```
def daniel.gender
    "male"
end
```

This method is certainly not stored in the class' method table as an instance method, as other instances would then be able to access it. However, this does not work:

```
p daniel.gender #=> "male"
p bonnie.gender #=> undefined method `gender' for #<MyClass:0x0000000005052a30 @occupation="Lawyer"> (NoMethodError)
```

So what's really going on? Ruby creates a hidden class, that is, a **metaclass**. The metaclass contains a new method table with its unique methods in it, as well as a superclass reference. That is, the object is no longer directly related to the parent class but through its metaclass.

| Object(daniel) | MetaClass(daniel) | MyClass |
| -------- | -------- | -------- |
| class reference =>     | methods table (*#gender*)      |      |
|      | superclass reference =>    | methods table   |

Thus, different instances share the same class, but each has its own metaclass. That is why we call them *singleton classes*.

It is noted that the metaclass is hidden, so the `#ancestors` method will not show it us as part of the chain.

This process (creating a custom method on an instance) is, of course, pretty weird and probably rare. However, there is another process that we use all the time and just works the same way: *creating class methods*.

When we create a class method, that method cannot be stored in the method table of the class’ class, because then it would become available to each subclass. Thus, Ruby creates a metaclass that stores the methods that only the given class needs in its own, unique method table.

```
class MyClass
    #...

    def self.print_my_job
        "I'm an instance factory"
    end

    #...
end
```

| MyClass | MetaClass(MyClass) | Class |
| -------- | -------- | -------- |
| class reference =>     | methods table (*#print_my_job*)      |      |
|      | class reference =>    |   | |

The above means that actually *all class methods are instance methods in their own way.*

**What about the modules?**

When you *include* a module in a class, the background process is very similar but not exactly the same. Ruby creates a special **IncludeClass** for us that is included in the ancestor chain and refers to the module's method table. It is important that when we include a module, we do not simply copy the methods from the module but refer to the memory space where the module methods are stored. This means that if we add another method to the module after we included the module in our class, the method becomes available to the class immediately.

| MyClass | IncludeClass(MyModule) | MyModule |
| -------- | -------- | -------- |
|  superclass reference =>    |    |   |
|      | methods table =>   | methods table(#new_method)  |

By using include, the methods in the IncludeClass' methods table (referred by the class) become instance methods for the class. When using extend, however, the methods from the module’s method table become methods in the singleton (meta)class of the class, that is, they appear as class methods for us (just like we saw it above).

If you both have instance and class methods in your module that you want to include in the class, one thing you can do to simplify the process is using the `#self.included` method.

```
module MyModule
    def inst_meth
       puts "instance_method"
    end
   
    def self.included(klass)
       klass.extend ClassMethods
    end
   
    module ClassMethods
      def class_meth
        puts "class method"
      end
    end
end
```

The `#self.included` method gets called whenever a module is included in another class. Ruby passes the class (in which the module is included) as an argument and runs the code in the block. By using this technique you do not need to separately include your instance methods and then extend your class methods by referring the module’s namespace, it is enough if you simply include the module in the class:

```
class MyClass
    include MyModule
    #...
end

p MyClass.class_meth #=> "class method"
```

**Key takeaways:**

* Ruby uses metaclasses under the hood to provide us with an object model with class inheritance and mechanisms to include code from modules
* Objects don't have methods, classes do - instance methods are stored either in the parent class' or the instance's metaclass' methods table 
* All class methods are basically instance methods stored in the unique singleton class of the given class
* When including modules, Ruby creates a special metaclass that becomes part of the ancestor chain and refers to the module's method table

