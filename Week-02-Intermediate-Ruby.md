# Week 2 - Intermediate Ruby

## Topic Ideas
* Gems
* Bundler
* Methods
  * Calling with and without parenthesis
* Classes/Objects

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

## Moving on

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

        fred[:knows_html] || "Doesn't know html"  #=> "Doesn't know html"

        james[:knows_html] || "Doesn't know html"  #=> "Doesn't know html"

        james.fetch(:knows_html){ "No information provided" }

        ```
* Hash arguments

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


### Objects

### Classes
* Template for building an object.
* Defines what methods

* Duck Typing



## Homework
* Thoughtbot Intro to Rails - Advanced Ruby workshop
