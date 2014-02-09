# Week 1 - Intro to Ruby


## Intro

* Thank everyone for coming.
* Durango's first Rails school.
  * Inspired by GSchool, Hungry Academy, Dev Bootcamp
  * Goal to grow number of rails developers in Durango, so we can take on bigger projects and
    compete with the Front Range
* Focus on helping students become professional rails developers
  * No programming experience assumed
  * First few weeks are very basic, but will progress quickly.
  * There will be lots of homework. Students should be highly motivated.
* Free, but some resources are paid - up to $130/month


### Introduce Instructors

* Alexii Carey
  * Partner and lead developer at E7 Systems
* Dan Marlow
  * Developer on Blinker
  * Worked for GoDaddy
* Recognize Durango Tech members

### Student Introductions

1. Name
2. Background - what have they done before programming
3. What interests them about programming?
4. What previous programming experiences?

## What are Ruby and Rails?

### Ruby - programming language

* General purpose language like C++, Java. Can write command line programs, web servers.
  * Cross platform (Mac, Windows, Linux)
  * Reading files, downloading webpages
  * Bayfield Deployment manager
  	* Checked versions of installed software on lab machines
  	* Updated software if an update was available
  * Bayfield Email list sync
    * Extracted parent emails from PowerSchool and built email lists for the parents of
      each grade level (9thGradeParents@bayfield.k12.co.us)
* Easy to read syntax. Can do a lot more with a single line of code than other languates

```
5.times do
  send_email
end

fill_in "Name", with: "George"
fill_in "Email", with: "aaron@example.com"
click_on "Save"


sparky = Dog.new name: "Sparky"
sparky.speak
#=> "Sparky says hi"

```

### Rails - framework for building web applications

* Designed to help build web applications
  * Handling url incoming request
  * Rendering HTML
  * Saving data, web forms, required fields
  * Sessions - Logins
* Web Applications vs Web Sites
* Sample  sites
  * Twitter
  * Github
  * Shopify
  * Hulu
  * CloseoutBike & Rebill
* Establishes conventions on how to build and organize sites
  * Takes away the guess work of "Where should this go?"
  * Makes it easy to look at a new rails app and figure out what's going on
* Lots of libraries to add functionality
  * Clearance, Devise - Authentication
  * ActionMailer - sending emails
  * Geocoder - Detecting user location

## Technologies We'll Cover

* Ruby
* Rails
* HTML, CSS
* Databases / SQL
* Git and Source Control
* Test Driven Development
* Agile Development
* Much more as the come along

(From http://devbootcamp.com/learn-more/)


## Break


## Setting Up Accounts

* Github - Use a professional username
* Nitrous.io - Link on website. Sign up with Github account
* Thoughtbot Learn - Sign up with Github Account. Learn Workshops
* Code School - Sign up with Github Account. No need to do paid enrollment yet.


## Trying out Ruby

1. Install Nitrous.IO Chrome desktop app. Link on site
2. Create new Ruby on Rails box. This is your development machine from this point forward. No need to
   set up more boxes
3. Click on IDE view for your box
4. Get ruby version. Good to know when looking at documentation.

    ```
    action@durango-instructor-rails-76891:~$ ruby -v
    ruby 2.0.0p247 (2013-06-27 revision 41674) [x86_64-linux]
    ```
5. Create a file under ~ called hello.rb
6. Add the following code into hello.rb

    ```
    puts "Hello Ruby!"
    ```
    Save
7. In the terminal

    ```
    action@durango-instructor-rails-76891:~$ ruby hello.rb
    Hello Ruby
    ```

