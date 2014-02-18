# Week 2 - Intermediate Ruby and Git

## Questions from homework?

My solution

```
puts "Tell me a word, any word: "
word = gets.strip

if word == word.reverse
  puts "It's a palendrome!"
else
  puts "Nope, not a palendrome"
end
```

Could also downcase to do a case insensitive match.

## More Ruby

### Methods

* A chunk of code that can be run over and over
    * Parameters allow input
    * Return values allow output

    ```
    # Orignally
    puts "Hello Larry!"
    puts "Hello George!"

    # New
    def greeting(name)
        "Hello #{name}!"
    end

    puts greeting("Larry")
    puts greeting("George")
    ```

    ```
    # Original
    words = ["fred","racecar"]

    words.each do |w|
        if w == w.reverse
        puts "It's a palendrome!"
        else
        puts "Nope, not a palendrome"
        end
    end

    # New
    def is_palendrome?(word)
        word == word.reverse
    end

    words = ["fred","racecar"]

    words.each do |w|
        if is_palendrome?(word)
        puts "It's a palendrome!"
        else
        puts "Nope, not a palendrome"
        end
    end
    ```
* last line of method is what is returned.
* Simple methods can be called with or without parenthises

  ```
  is_palendrome?(word)
  is_palendrome? word

  def print_palendrome_message palendrome_status, success_message, failure_message
    if palendrome_status
      puts success_message
    else
      puts failure_message
    end
  end

  print_palendrome_message is_palendrome? word, "Yep", "Nope"
  print_palendrome_message is_palendrome?(word), "Yep", "Nope"
  ```

* Default arguments

    ```
      def print_palendrome_message(palendrome_status, success_message = "Yep", failure_message = "Nope")
        if palendrome_status
          puts success_message
        else
          puts failure_message
        end
      end

      result = is_palendrome? "Fred"
      print_palendrome_message result
    ```
* Hash arguments

    ```
    def print_palendrome_message(palendrome_status, options={})
      # Implementation here
    end

    result = is_palendrome? "Fred"
    print_palendrome_message result, success: "Yep", failure: "Nope"
    # Same as print_palendrome_message(result, {success: "Yep", failure: "Nope"})
   ```
* Detour into hashes, symbols and default values
    * Symbols - simple strings. Mainly used for hash keys

        ```
        string_hash = { "name" => "Fred", "age" => 35 }
        string_hash["name"]

        symbol_hash = { :name => "Fred", :age => 35 }
        symbol_hash[:name]

        # Ruby 1.9+ hash syntax
        {name: "Fred", age: 35}

        ```
     * Comparisons

        ```
        def is_chosen?(person)
          if person[:name] == "Fred" || person[:age] > 30
            true
          else
            false
          end
        end

        is_chosen? {name: "Fred", age: 28 } #=> true
        is_chosen? {name: "George", age: 58 } #=> true
        is_chosen? {name: "Amy", age: 22 } # false

        # Can rewrite method to
        def is_chosen?(person)
          name == "Fred" || age > 30
        end

        ```
        * Logical Operators
            * && - And - both values have to be true. If first value is false, it doesn't try the second comparison
            * || - Or - one of the values has to be true. If the first value is true, it doesn't try the rest.
            * ! - Not
        * Use parenthesis for grouping
        * [Comparison cheat sheet](http://www.tutorialspoint.com/ruby/ruby_operators.htm)
     * Truthy or falsey

        ```
        def truthy_or_falsey(value)
          if value
            "truthy"
          else
            "falsey"
          end
        end

        puts truthy_or_falsey nil
        ```
        * Truthy - "word", "", [], 0, true
        * Falsey - nil, false
     * Default hash values

        ```
        fred = {name: "Fred", age: 35, knows_html: false }
        james = {name: "James", age: 24, job: "Lead developer" }

        fred[:job] #=> nil

        fred[:job] || "No job given" #=> "No job given"

        james[:knows_html] || "No information provided"  #=> "No information provided"
        james[:knows_html] || "No information provided"  #=> "No information provided"

        james.fetch(:knows_html){ "No information provided" } #=> false
        ```
* Bringing it all together

    ```
    def print_palendrome_message(palendrome_status, options={})
      success_message = options[:success] || "Yep"

      #Another version of default assignment
      failure_message = options[:failure]
      failure_message ||= "Nope"  # Sames as failure_message = failure_message || "Nope"

      # Could also use failure_message = options.fetch(:failure){ "Nope" }

      if palendrome_status
        puts success_message
      else
        puts failure_message
      end
    end

    result = is_palendrome? "Fred"
    print_palendrome_message result, success: "Yep", failure: "Nope"
   ```


### Objects and classes
* Class
    * Template for building an object
    * Defines what methods are available
    * Look at [String documentation](http://www.ruby-doc.org/core-2.0.0/String.html)
* Object
    * Instance of a class
    * Usually created by class.new
    * Everything is an object. Objects have methods and a class

        ```
        "Hello world".class # => String
        "Hello world".class.class # => Class

        "Hello world".methods #=> [:<=>, :==, :===, :eql?, :hash, :casecmp, ...]
        ```
    * Constructors

        ```
        a = Array.new 5
        puts a.inspect #=> [nil, nil, nil, nil, nil]

        b = ["javasript", "ruby"] # This is a "literal". It also creates a new array, but that is done by ruby
        ```

* Creating our own classes (Bookstore App)
    * First try (using hashes)

        ```
        book_1 = {title: "Programming Ruby", pages: 360}

        ```
        * no guarentee that pages any of the fields will be defined
        * What if you wanted to add a generated summary?
    * Using a class

        ```
        class Book

          def initialize(title, pages)
            @title = title
            @pages = pages
          end

          def title
            @title
          end

          def pages
            @pages
          end

        end

        book_1 = Book.new "Programming Ruby", 360

        puts book_1.name #=> "Programming Ruby"
        puts book_1.pages #=> 360
        ```
     * Adding generated data

        ```
        class Book

          # Previous implementation omitted

          def summary
            "#{title}" is #{pages} pages
          end

        end

        book_1 = Book.new "Programming Ruby", 360

        puts book_1.summary #=> "Programming Ruby is 360 pages"
        ```
     * Assignment methods

        ```
        class Book

          # Previous implementation omitted

          def pages=(value)
            @pages = value
          end

        end

        book_1 = Book.new "Programming Ruby", 360

        puts book_1.summary #=> "Programming Ruby is 360 pages"

        book_1.pages = 241
        puts book_1.summary #=> "Programming Ruby is 241 pages"
        ```
     * Generating getters and setters

        ```
        class Book
          def initialize(title, pages)
            @title = title
            @pages = pages
          end

          def title
            @title
          end

          def title=(value)
            @title = value
          end

          def pages
            @pages
          end

          def pages=(value)
            @pages = value
          end

          def summary
            "#{title} is #{pages} pages"
          end
        end

        # Can be replaced with

        class Book
          attr_accessor :title, :pages

          def initialize(title, pages)
            @title = title
            @pages = pages
          end

          def summary
            "#{title} is #{pages} pages"
          end
        end
        ```
        * Show module, attr_reader, attr_writer, attr_accessor


    * Class vs instance methods
        * Show Array API Docs
        * :: are class methods
        * \# are instance methods

        ```
        a = Array.new(3) #=> [nil, nil, nil]
        a.length => 3

        a.new #=> NoMethodError: undefined method `new' for [nil, nil, nil]:Array
        Array.length #=> NoMethodError: undefined method `length' for Array:Class
        ```
    * Duck Typing
        * If it quacks like a duck, it must be a duck

        ```
        class Book
          attr_accessor :title, :pages

          def initialize(title, pages)
            @title = title
            @pages = pages
          end

          def summary
            "#{title} is #{pages} pages"
          end
        end

        class Drink
          attr_accessor :brand, :size

          def initialize(brand, size)
            @brand = brand
            @size
          end

          def summary
            "#{size} #{brand}"
          end
        end

        book_1 = Book.new "War, what is it good for", 360
        book_2 = Book.new "The Bible", 2500
        drink = Drink.new "Coke", "8 oz"

        [book_1, coffee, book_2].each do |item|
          puts item.summary
        end
        ```
    * Inheritance

        ```
        class Coffee < Drink
          def summary
            "Hot #{size} #{brand} coffee"
          end
        end
        ```

* Collecting summaries in a single array

  ```
  summaries = [book_1, coffee, book_2].collect do |item|
    item.summary
  end

  # Shorthand
  summaries = [book_1, coffee, book_2].collect(&:summary)
  ```

## Unix commands

* Common unix commands
    * cd - change dir
    * mkdir - make a directory
    * rm - remove file or directory
    * touch - create a blank file
    * cat - print out a file
    * man - man pages for a command

* ~ Your home directory

## Git

* Distributed Version Control System (DVCS)
* Let's you track the changes you've made and allows rolling back easily
* Used by github, Heroku.

```
git clone https://github.com/durango-ruby-school/Instructor-Notes.git
cd Instructor-Notes

git log

git checkout -b test

edit README

git status

git add .

git commit -m "A test commit"

git log

git push origin <BRANCHNAME>

Login to git and make pull request

```



## Homework
* Thoughtbot Intro to Rails - Advanced Ruby workshop
* Code School's Try Git
