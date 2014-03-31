# Week 7 - Postgres and Intro to TDD


## Postgres

* Open source database used by heroku
* Great database with lots of plugins including PostGIS

### Installing Postgres

1. Install and start postgres

    ```
    $ parts install postgresql

    $ parts start postgresql
    ```

    *Will have to run `parts start postgresql` every time you log in to your machine*
2. Create a new super user to connect to the db as

    ```
    $ createuser --interactive rails
    Shall the new role be a superuser? (y/n) y
    ```

### Create a rails 4 app and set postgres as default database

1. Upgrade rails

    ```
    $ gem update rails
    $ rails -v
    Rails 4.0.4
    ```
2. Create your new rails app with postgres as the default database. (-T means skip test unit files)

    ```
    $ cd workspace
    $ rails new readathon -d postgresql -T
    $ cd readathon
    ```
3. Rename README.rdoc to README.md
4. Enter some info about your app

    ```
    # Read-A-Thon

    This is a sample app built in Durango Ruby School to help local kids with their schools Readathons
    ```
5. We don't want to check in our database.yml because it could contain passwords, or info specific to your dev machine. Let's make sure its never checked in

      1. Click show hidden. Edit .gitignore and add this line

          ```
          /config/database.yml
          ```

      2. Rename database.yml to database.example.yml

          ```
          $ mv config/database.yml database.example.yml
          ```

      3. Update database.example.yml to look like this

          ```
          defaults: &defaults
            adapter: postgresql
            encoding: unicode
            pool: 5 # Should be set to the number of sidekiq concurrency + web workers
            username: rails
            password:
            host: localhost


          development:
            <<: *defaults
            database: readathon_development

          # Warning: The database defined as "test" will be erased and
          # re-generated from your development database when you run "rake".
          # Do not set this db to the same as development or production.
          test:
            <<: *defaults
            database: readathon_test
            min_messages: WARNING

          production:
            <<: *defaults
            database: readathon_production
          ```

      4. Update your readme to include the following setup instructions

          ```
          ## Setup Instructions

          1. Copy config/database.example.yml to config/database.yml
          2. Update config/database.yml to work with your local machine
          ```

6. Check in your changes

    1. Create a new git repo

        ```
        git init .
        ```

    2. Stage your changes

        ```
        $ git add .
        ```

    3. Check to see what will be checked in

        ```
        $ git status
        ```

        Make sure config/database.example.yml is going to be checked in, but not config/database.example.yml

    4. Commit your changes

        ```
        $ git commit -m "Initial checkin"
        ```

7. Copy database.example.yml to database.yml

    ```
    $ cp config/database.example.yml config/database.yml
    ```

8. Run git status and make sure it shows no changes (since you ignored config/database.yml earlier)

    ```
    $ git status
    # On branch master
    nothing to commit, working directory clean
    ```

9. Create your development database

    ```
    $ rake db:create
    ```
10. Run rake db:migrate to set up the initial database

    ```
    $ rake db:migrate
    ```

11. Run git status and see that it has generated the initial schema.rb file
12. Commit

    ```
    $ git add .
    $ git commit -m "Initial schema"
    ```
13. Start up your app and make sure it works.

    ```
    $ bundle exec rails s
    ```

## Test Driven Development

### Why test?

* To ensure the application works as expected
* Help you develop better code
* Ensure future changes don't break your code
  * Makes sure refactoring code doesn't break exisiting functionality
* Catch issues before they hit the user (ie. upgrading rails might cause issues that you might not expect)

Red, Green, Refactor

1. Write a failing test
2. Write the code to get the test to pass
3. Rework the code to make it more flexible, or easier to read without breaking it.

Outside-in Development

1. Outside - Write high level tests first that ensures you can complete a specific function (create a blog post)
2. In - Write low level tests, like ensuring a blog post has a title to cover all of the edge cases

Integration tests - the outside part. Use rspec and capybara
Unit Tests - the inside part. Use rspec

### Installing rspec

1. Add the following to your Gemfile

    ```
    group :development, :test do
      gem 'rspec-rails'
    end
    ```

2. Bundle install

    ```
    $ bundle
    ```

3. Run rspec install generator

    ```
    $ rails generate rspec:install
          create  .rspec
          create  spec
          create  spec/spec_helper.rb
    ```

4. Run rspec

    ```
    $ rspec
    No examples found.


    Finished in 0.00006 seconds
    0 examples, 0 failures
    ```

5. Commit

    ```
    $ git add .
    $ git commit -m "Installed rspec"
    ```

### Writing your first test

1. Create spec/features/view_homepage_spec.rb

    ```
    require 'spec_helper'
    ```

2. Run the spec

    ```
    $ rspec spec/features/view_homepage_spec.rb
    No examples found.


    Finished in 0.00018 seconds
    0 examples, 0 failures

    Randomized with seed 31125
    ```

3. Add capybara to the Gemfile

    ```
    group :development, :test do
      gem 'rspec-rails'
      gem 'capybara'
    end
    ```
4. Bundle install

    ```
    $ bundle
    ```

5. Write the first spec in spec/feature/view_homepage_spec.rb

    ```
    require 'spec_helper'

    feature "View the homepage" do
      scenario "user sees correct information" do
        visit root_path

        expect(page).to have_content "Welcome"
        expect(page).to have_title "Readathon App"
      end
    end
    ```


6. Run the spec

    ```
    $ rspec spec/features/view_homepage_spec.rb
    F

    Failures:

      1) View the homepage user sees correct information
         Failure/Error: visit root_path
         NameError:
           undefined local variable or method `root_path' for #<RSpec::Core::ExampleGroup::Nested_1:0x00000004407000>
         # ./spec/features/view_homepage_spec.rb:5:in `block (2 levels) in <top (required)>'

    Finished in 0.00238 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/view_homepage_spec.rb:4 # View the homepage user sees correct information

    Randomized with seed 61940
    ```
7. Create a route for the root path in config/routes.rb

    ```ruby
    Readathon::Application.routes.draw do
      root 'pages#index'
    end
    ```

8. Run the spec

    ```
    $ rspec spec/features/view_homepage_spec.rb
    F

    Failures:

      1) View the homepage user sees correct information
         Failure/Error: visit root_path
         ActionController::RoutingError:
           uninitialized constant PagesController
         # ./spec/features/view_homepage_spec.rb:5:in `block (2 levels) in <top (required)>'

    Finished in 0.01884 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/view_homepage_spec.rb:4 # View the homepage user sees correct information

    Randomized with seed 38271
    ```

9. Create the pages controller (app/controllers/pages_controller.rb)

    ```ruby
    class PagesController < ApplicationController
    end
    ```

10. Run the spec

    ```
    $ rspec spec/features/view_homepage_spec.rb
    F

    Failures:

      1) View the homepage user sees correct information
         Failure/Error: visit root_path
         AbstractController::ActionNotFound:
           The action 'index' could not be found for PagesController
         # ./spec/features/view_homepage_spec.rb:5:in `block (2 levels) in <top (required)>'

    Finished in 0.03785 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/view_homepage_spec.rb:4 # View the homepage user sees correct information

    Randomized with seed 14984
    ```

11. Create the index action in the pages controller

    ```ruby
    class PagesController < ApplicationController
      def index
      end
    end
    ```

12. Run the spec

    ```bash
    $ rspec spec/features/view_homepage_spec.rb
    F

    Failures:

      1) View the homepage user sees correct information
         Failure/Error: visit root_path
         ActionView::MissingTemplate:
           Missing template pages/index, application/index with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :jbuilder, :coffee]}. Sear
    ched in:
             * "/home/action/workspace/readathon/app/views"
         # ./spec/features/view_homepage_spec.rb:5:in `block (2 levels) in <top (required)>'

    Finished in 0.06525 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/view_homepage_spec.rb:4 # View the homepage user sees correct information

    Randomized with seed 21833
    ```

13. Create app/views/pages/index.html.erb

    ```html
    <h1>Hello</h1>
    ```

14. Run the spec

    ```bash
    $ rspec spec/features/view_homepage_spec.rb
    F

    Failures:

      1) View the homepage user sees correct information
         Failure/Error: expect(page).to have_content "Welcome"
           expected to find text "Welcome" in "Hello"
         # ./spec/features/view_homepage_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 1.03 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/view_homepage_spec.rb:4 # View the homepage user sees correct information

    Randomized with seed 25435
    ```

15. Edit app/views/pages/index.html.erb

    ```html
    <h1>Welcome</h1>
    ```

16. Run the spec

    ```bash
    $ rspec spec/features/view_homepage_spec.rb
    F

    Failures:

      1) View the homepage user sees correct information
         Failure/Error: expect(page).to have_title "Readathon App"
           expected there to be title "Readathon App" in "Readathon"
         # ./spec/features/view_homepage_spec.rb:9:in `block (2 levels) in <top (required)>'

    Finished in 0.60785 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/view_homepage_spec.rb:4 # View the homepage user sees correct information

    Randomized with seed 42438
    ```

17. Fix the title in app/views/layouts/application.html.erb

    ```
    <!DOCTYPE html>
    <html>
    <head>
      <title>Readathon App</title>
      <%= stylesheet_link_tag    "application", media: "all", "data-turbolinks-track" => true %>
      <%= javascript_include_tag "application", "data-turbolinks-track" => true %>
      <%= csrf_meta_tags %>
    </head>
    <body>

    <%= yield %>

    </body>
    </html>
    ```

18. Run the spec

    ```bash
    $ rspec spec/features/view_homepage_spec.rb
    .

    Finished in 0.11089 seconds
    1 example, 0 failures

    Randomized with seed 54230
    ```

19. Commit

    ```bash
    $ git add .
    $ git commit -m "View homepage feature"
    ```

### Managing Books

1. Write a new spec spec/features/book_management_spec.rb

    ```ruby
    require "spec_helper"
    ```

2. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    No examples found.


    Finished in 0.00018 seconds
    0 examples, 0 failures

    Randomized with seed 64289
    ```

3. Write the following spec

    ```
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path

        click_link "Books"
        click_link "Add book"

        fill_in "Title", with: "Garfield"
        fill_in "Pages", with: "250"

        click_button "Save"

        expect(page).to have_content "Garfield"
        expect(page).to_not have_content "Save"
      end
    end
    ```

4. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Books"
         Capybara::ElementNotFound:
           Unable to find link "Books"
         # ./spec/features/book_management_spec.rb:7:in `block (2 levels) in <top (required)>'

    Finished in 0.12098 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 59663
    ```

5. Create the book link in app/views/layouts/application.html.erb

    ```
    <!DOCTYPE html>
    <html>
    <head>
      <title>Readathon App</title>
      <%= stylesheet_link_tag    "application", media: "all", "data-turbolinks-track" => true %>
      <%= javascript_include_tag "application", "data-turbolinks-track" => true %>
      <%= csrf_meta_tags %>
    </head>
    <body>
      <%= link_to "Books", books_path %>

    <%= yield %>

    </body>
    </html>
    ```

6. Run the spec

    ```
    rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: visit root_path
         ActionView::Template::Error:
           undefined local variable or method `books_path' for #<#<Class:0x00000003f99ef0>:0x00000003f990b8>
         # ./app/views/layouts/application.html.erb:10:in `_app_views_layouts_application_html_erb___471408764387068500_33711640'
         # ./spec/features/book_management_spec.rb:5:in `block (2 levels) in <top (required)>'

    Finished in 0.10605 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 26005
    ```

7. Create the books route in config/routes.rb

    ```ruby
    Readathon::Application.routes.draw do
      root 'pages#index'

      resources :books, only: [:index]
    end
    ```

8. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Books"
         ActionController::RoutingError:
           uninitialized constant BooksController
         # ./spec/features/book_management_spec.rb:7:in `block (2 levels) in <top (required)>'

    Finished in 0.11626 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 23749
    ```

9. Create the books controller (app/controllers/books_controller.rb)

    ```ruby
    class BooksController < ApplicationController
    end
    ```

10. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Books"
         AbstractController::ActionNotFound:
           The action 'index' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:7:in `block (2 levels) in <top (required)>'

    Finished in 0.11503 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 23501
    ```

11. Create the index action

    ```ruby
    class BooksController < ApplicationController
      def index
      end
    end
    ```

12. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Books"
         ActionView::MissingTemplate:
           Missing template books/index, application/index with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :jbuilder, :coffee]}. Searched in:
             * "/home/action/workspace/readathon/app/views"
         # ./spec/features/book_management_spec.rb:7:in `block (2 levels) in <top (required)>'

    Finished in 0.11185 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 29838
    ```

13. Create a blank app/views/books/index.html.erb
14. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Add book"
         Capybara::ElementNotFound:
           Unable to find link "Add book"
         # ./spec/features/book_management_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 0.10983 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 2364
    ```

15. Create a an add book link in app/views/books/index.html.erb

    ```
    <%= link_to "Add book", new_book_path %>
    ```

16. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Books"
         ActionView::Template::Error:
           undefined local variable or method `new_book_path' for #<#<Class:0x00000002ac9188>:0x00000002ad26c0>
         # ./app/views/books/index.html.erb:1:in `_app_views_books_index_html_erb___2762012422487341792_22495960'
         # ./spec/features/book_management_spec.rb:7:in `block (2 levels) in <top (required)>'

    Finished in 0.623 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 39019
    ```

17. Create the new book route in config/routes.rb

    ```ruby
    Readathon::Application.routes.draw do
      root 'pages#index'

      resources :books, only: [:index, :new]
    end
    ```

18. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Add book"
         AbstractController::ActionNotFound:
           The action 'new' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 0.12338 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 4326
    ```

19. Create the new action in app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
      end
    end
    ```

20. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Add book"
         ActionView::MissingTemplate:
           Missing template books/new, application/new with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :jbuilder, :coffee]}. Searched in:
             * "/home/action/workspace/readathon/app/views"
         # ./spec/features/book_management_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 0.62012 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 431
    ```

21. Create an empty app/views/books/new.html.erb
22. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: fill_in :title, with: "Garfield"
         Capybara::ElementNotFound:
           Unable to find field :title
         # ./spec/features/book_management_spec.rb:10:in `block (2 levels) in <top (required)>'

    Finished in 0.11956 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 4341
    ```

23. Create the form

    1. Add simpleform to the Gemfile

        ```
        gem 'simple_form'
        ```

    2. Bundle install

        ```bash
        $ bundle install
        ```

    3. Update app/views/books/new.html.erb

        ```
        <%= simple_form_for @book do |f| %>
          <%= f.input :title %>
          <%= f.input :pages %>

          <%= f.submit "Save" %>
        <% end %>
        ```

24. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Add book"
         ActionView::Template::Error:
           undefined method `model_name' for NilClass:Class
         # ./app/views/books/new.html.erb:1:in `_app_views_books_new_html_erb___905899577793093027_28547520'
         # ./spec/features/book_management_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 0.22834 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 35141
    ```
25. Update app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end
    end
    ```

26. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Add book"
         NameError:
           uninitialized constant BooksController::Book
         # ./app/controllers/books_controller.rb:6:in `new'
         # ./spec/features/book_management_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 0.11718 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 12632
    ```

27. Create the book model

    1. Generate the model

        ```bash
        $ rails g model book title pages:integer
              invoke  active_record
              create    db/migrate/20140330223603_create_books.rb
              create    app/models/book.rb
              invoke    rspec
              create      spec/models/book_spec.rb
        ```

    2. Migrate the database (and setup the test database)

        ```bash
        $ rake db:migrate db:test:prepare
        == 20140330223603 CreateBooks: migrating ======================================
        -- create_table(:books)
           -> 0.2195s
        == 20140330223603 CreateBooks: migrated (0.2197s) =============================
        ```

28. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActionController::RoutingError:
           No route matches [POST] "/books"
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.24519 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 59351
    ```

29. Update config/routes.rb to have a :create action

    ```ruby
    Readathon::Application.routes.draw do
      root 'pages#index'

      resources :books, only: [:index, :new, :create]
    end
    ```

30. Run the spec

    ```ruby
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         AbstractController::ActionNotFound:
           The action 'create' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.22995 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 42682
    ```

31. Add the create action to the books controller

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end

      def create
      end
    end
    ```

32. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActionView::MissingTemplate:
           Missing template books/create, application/create with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :jbuilder, :coffee]}. Searched in:
             * "/home/action/workspace/readathon/app/views"
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.74083 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 1865
    ```

33. Fill in the create action

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end

      def create
        @book = Book.new params[:book]
        if @book.save
          redirect_to @book
        else
          render "new"
        end
      end
    end
    ```

34. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActiveModel::ForbiddenAttributesError:
           ActiveModel::ForbiddenAttributesError
         # ./app/controllers/books_controller.rb:10:in `create'
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.73036 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 56075
    ```

35. Permit the attributes using strong parameters

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end

      def create
        @book = Book.new book_params
        if @book.save
          redirect_to @book
        else
          render "new"
        end
      end

      private

      def book_params
        params.require(:book).permit(:title, :pages)
      end
    end
    ```

36. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         NoMethodError:
           undefined method `book_url' for #<BooksController:0x000000043ae978>
         # ./app/controllers/books_controller.rb:12:in `create'
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.29652 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 1126
    ```

37. Add the show route for books in config/routes.rb

    ```ruby
    Readathon::Application.routes.draw do
      root 'pages#index'

      resources :books, only: [:index, :new, :create, :show]
    end
    ```

38. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         AbstractController::ActionNotFound:
           The action 'show' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.75141 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 53799
    ```

39. Create the show action in app/controllers/books_controller.rb

    ```
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end

      def show
      end

      def create
        @book = Book.new book_params
        if @book.save
          redirect_to @book
        else
          render "new"
        end
      end

      private

      def book_params
        params.require(:book).permit(:title, :pages)
      end
    end
    ```

40. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActionView::MissingTemplate:
           Missing template books/show, application/show with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :jbuilder, :coffee]}. Searched in:
             * "/home/action/workspace/readathon/app/views"
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.75007 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 35501
    ```

41. Create an empty app/books/views/show.html.erb
42. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: expect(page).to have_content "Garfield"
           expected to find text "Garfield" in "Books"
         # ./spec/features/book_management_spec.rb:15:in `block (2 levels) in <top (required)>'

    Finished in 0.75184 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 55177
    ```

43. Fill out app/views/books/show.html.erb

    ```
    <h1><%= @book.title %><h1>

    <p>This book has <%= @book.pages %></p>
    ```

44. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActionView::Template::Error:
           undefined method `title' for nil:NilClass
         # ./app/views/books/show.html.erb:1:in `_app_views_books_show_html_erb__3179130953602822843_34063580'
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.7481 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 34673
    ```

45. Update the show action in the books controller

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end

      def show
        @book = Book.find params[:id]
      end

      def create
        @book = Book.new book_params
        if @book.save
          redirect_to @book
        else
          render "new"
        end
      end

      private

      def book_params
        params.require(:book).permit(:title, :pages)
      end
    end
    ```

46. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    .

    Finished in 0.74807 seconds
    1 example, 0 failures

    Randomized with seed 19101
    ```

47. WooHoo!!! Commit it!

    ```bash
    $ git add .
    $ git commit -m "Add book functionality"
    ```

### Let's see what we built

1. Fire up the server

    ```bash
    $ bundle exec rails s
    ```


## Deploying on heroku

The heroku toolbelt is already installed on Nitrous by default

1. Sign up for a heroku account, if you don't have one already.
2. Create the app from your command line

    ```
    $ heroku apps:create <your-initials>-readathon
    Enter your Heroku credentials.
    Email: you@example.com
    Password (typing will be hidden):
    Creating test-readathon... done, stack is cedar
    http://test-readathon.herokuapp.com/ | git@heroku.com:test-readathon.git
    Git remote heroku added
    ```
3. Run git remote to see a new heroku remote has been added.

    ```
    $ git remote
    heroku
    ```
4. Upload your ssk key so you can deploy. Only need to do this the first time you push to heroku from your computer

    ```
    $ heroku keys:add
    ```

4. Deploy to heroku

    ```
    $ git push heroku master
    ```

5. Go to http://\<your-initials\>-readathon.herokuapp.com

