---
layout: post
title:      "Creating Your Own Documentation With Rdoc"
date:       2020-05-19 23:56:10 +0000
permalink:  creating_your_own_documentation_with_rdoc
---


As the applications we build become more complex, it may be worthwhile to create our own documentation for a particular project, instead of reading the source code directly.

Luckily, this is a lot simpler than you might think, since a very useful Ruby gem, **Rdoc**, does the work for us. Rdoc is an *embedded documentation generator* that analyzes Ruby source code and produces a structured collection of pages for our Ruby objects and methods. By using it, a complex application with many classes and methods becomes very easy to review later.

Suppose we have a ruby file that has two classes, *BookShop* and *Author*. Both classes contain class and instance methods, include a module, and have instance variables.

```
class BookShop
    include Enumerable
    attr_accessor :books

    def initialize
        @books = []
    end

    def add_book(book_name)
        @books << book_name
    end

    def print_all
        @books.each {|b| puts b}
    end

    def self.my_job
        "I am creating book shops"
    end
end

class Author
    include Enumerable
    attr_accessor :name, :my_books

    @my_books = []

    def initialize(name)
        @name = name
    end

    def print_all_books
        @my_books.each {|b| puts b}
    end

    def self.my_job
        "I am creating authors"
    end
end
```

To create your custom documentation, all you have to do is `cd` to your folder that contains the ruby file and use the `rdoc` command with that file name:

```
// ♥ rdoc rdoc_practice.rb
```

You will then see something like this in your terminal:

```
Parsing sources...
100% [ 1/ 1]  rdoc_practice.rb

Generating Darkfish format into /mnt/c/Users/ptama/Desktop/rdoc/doc...

  Files:       1

  Classes:     2 ( 2 undocumented)
  Modules:     0 ( 0 undocumented)
  Constants:   0 ( 0 undocumented)
  Modules:     0 ( 0 undocumented)
  Constants:   0 ( 0 undocumented)
  Methods:     6 ( 6 undocumented)

  Total:      10 (10 undocumented)
    0.00% documented

  Elapsed: 0.2s
```

Rdoc tells us right away how many classes, attributes and methods it found and how many of them are undocumented at the moment. However, it is more important that Rdoc already created a new folder in our folder where we can access the html file that contains our new, structured documentation:

[Rdoc folder structure](https://drive.google.com/file/d/1U4si2Ep2jaxOEZYpr-aWKPjkqFdn3tYb/view?usp=sharing)

After opening the html file, the interface will look something like this:

[Rdoc documentation index page interface](https://drive.google.com/file/d/1en7G2IzPN7PS2i9f1E8OcjWeRr4ZWq1a/view?usp=sharing)

After clicking on one of the class names, it will look like this:

[Rdoc documentation class page interface](https://drive.google.com/file/d/1XdZqZ6hTX84YBo1WgNPffM0ZIW0BT1RF/view?usp=sharing)

We can see that Rdoc has broken down our Ruby file into classes, within which it has grouped our code into attributes(variables), class and instance methods. It also indicates the parent class and the modules that have been included.

If you click on one of the method names, you can see the associated method definition and also the line of the file where the given method was defined.

Rdoc is also smart enough to assign our comments to a particular method, so if, for example, I add a comment to one of the methods and run the `rdoc` command again, the comment will also appear in the documentation above that method:

```
# prints out the job of the class

    def self.my_job
        "I am creating book shops"
    end
```


[Commented method](https://drive.google.com/file/d/1qfcvhodVrIj_JVyGadm6aLkTaMMX2M0c/view?usp=sharing)

Another cool thing is that Rdoc accepts not only a file as a parameter, but also a folder. Thus, if I create a new file in my folder (*folder name in the example: rdoc*) that contains the following class:

```
class AnotherClass
    
    def i_only_have_one_method
        "It's me!"
    end
    
end
```

and then run the `rdoc` command with the folder name

```
// ♥  rdoc rdoc
```

the Rdoc gem adds all the classes (including that new class from the other file) to the documentation:

[New index page interface](https://drive.google.com/file/d/1BxL4uuTnb9l6MK0wPlJngdzTDdSqVV0v/view?usp=sharing)

[New class page interface](https://drive.google.com/file/d/1cymSRZb_kSUnc71WCmt6vjwVQudjQvQG/view?usp=sharing)

As you can see, Rdoc is a powerful gem that makes it very easy to create your own documentation and helps you to maintain a clear file and class structure in your project.

If you want to learn more about it, you can check the documentation here:

[Rdoc documentation](https://ruby.github.io/rdoc/)

