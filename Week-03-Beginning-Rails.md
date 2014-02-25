# Week 3 - Intro to Rails

## Installing Rails

```
$ gem install rails -v 3.2.17

```

## New Rails Project

```
$ rails -v
Rails 4.0.0
$ rails _3.2.17_ -v
Rails 3.2.17

$ cd workspace
$ rails _3.2.17_ new first_site
$ cd first_site
```

1. Look over Gemfile.
    1. Show versions
    2. Show how gem is just a method call
    3. Talk about bundle exec
2. Start up server with `bundle exec rails server`
3. git init, git commit

## HTML/CSS

See Slides

## Creating a Rails page

1. Create the pages controller in app/controllers/pages_controller.rb

    ```
    class PagesController < ApplicationController

      def home
      end

    end
    ```
2. Go into config/routes.rb and make it look like this:

    ```
    FirstSite::Application.routes.draw do
      get "/home" => "pages#home"
    end
    ```
3. Visit /home and see "Missing template pages/home message"
4. Create new file in app/views/pages/home.html.erb

    ```
    <h1>Hello Rails</h1>
    ```
5. As a class, add an about page following the same steps
6. View source, talk about page template
7. Show app/views/layouts/application.html.erb
8. Change the body of application.html.erb to add content to the template

    ```
    <body>
      <div>
        My cool header
      </div>

      <%= yield %>

      <div>
        My cool footer
      </div>
    </body>
    ```
9. Set up root url
    1. Delete public/index.html
    2. Set up root route
    ```
    FirstSite::Application.routes.draw do
      get "/about" => "pages#about"
      root to: "pages#home"
    end
    ```

## ERB

Embedded Ruby

* Output Tag

  ```
  <%= Time.now %>
  ```
* Loops

  ```
  <ul>
    <% %w(dog cat mouse).each do |animal| %>
      <li><%= animal %></li>
    <% end %>
  </ul>
  ```
* Instance Variables

  ```
  class PagesController < ApplicationController
    def index
      @animals = %w(dog cat mouse)
    end
  end
  ```

  ```
  <ul>
    <% @animals.each do |animal| %>
      <li><%= animal %>
    <% end %>
  </ul>
  ```
* Parameters

    ```
    class PagesController < ApplicationController
      def index
        @name = params[:name]
      end
    end
    ```
    ```
    <% if @name %>
    <p>Hello <%= @name %>
    <% end %>

    <form>
      <label>Name</label>
      <input type="input" name="name" />

      <input type="submit" />
    </form>
    ```



## Boostrap (not covered, ran out of time)

https://github.com/twbs/bootstrap-sass

1. Add bootstrap sass to gemfile in assets group

    ```
    gem 'bootstrap-sass', '~> 3.1.1'
    ```
2. Stop server, then `bundle install`
3. Rename app/assets/stylesheets/application.css to app/assets/stylesheets/application.css.scss

    ```
    @import "bootstrap";
    ```
4. Add Bootstrap Javascript to app/assets/javascripts/application.js

    ```
    //= require bootstrap
    ```
5. Create a button and style it

    ```
    <button type="button" class="btn btn-default">Default</button>
    ```
6. Add breadcrumbs

    ```
    <ol class="breadcrumb">
      <li><a href="#">Home</a></li>
      <li><a href="#">Library</a></li>
      <li class="active">Data</li>
    </ol>
    ```
7. Add Glyhicon

    ```
    <span class="glyphicon glyphicon-search"></span>
    ```
8. Add Navbar

    ```
      <div class="navbar navbar-default">
        <a href="/" class="navbar-brand">Durango Ruby School</a>

        <p class="navbar-text navbar-right">Signed in as Aaron Renner</p>
      </div>
    ```
9. Change the color of the navbar logo (in app/assets/stylesheets/application.css.scss)

    ```
    body{
      color: #660000;
    }
    ```
