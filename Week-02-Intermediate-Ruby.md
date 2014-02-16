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
* Detour into hashes, symbols and default values
* Hash arguments

    ```
    def print_palendrome_message(palendrome_status, options={})
      success_message = options[:success] || "Yep"

      failure_message = options[:failure]
      failure_message ||= "Nope"

      if palendrome_status
        puts success_message
      else
        puts failure_message
      end
    end

    result = is_palendrome? "Fred", success: "Yep", failure: "Nope"
    print_palendrome_message result
   ```


### Objects

### Classes
* Template for building an object.
* Defines what methods

* Duck Typing



## Homework
* Thoughtbot Intro to Rails - Advanced Ruby workshop
