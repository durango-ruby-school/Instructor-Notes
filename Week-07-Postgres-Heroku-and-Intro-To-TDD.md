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

    *Will have to run `parts start postgreql` every time you log in to your machine*
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
    $ rake db:migreate
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

    ```
    class PagesController < ApplicationController
      def index
      end
    end
    ```

12. Run the spec

    ```
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

    ```
    <h1>Hello</h1>
    ```

14. Run the spec

    ```
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

    ```
    <h1>Welcome</h1>
    ```

16. Run the spec

    ```
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

    ```
    $ rspec spec/features/view_homepage_spec.rb
    .

    Finished in 0.11089 seconds
    1 example, 0 failures

    Randomized with seed 54230
    ```

19. Commit

    ```
    $ git add .
    $ git commit -m "View homepage feature"
    ``



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



