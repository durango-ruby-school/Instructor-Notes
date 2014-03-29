# Week 7 - Postgres and Intro to TDD

## Why test?

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
