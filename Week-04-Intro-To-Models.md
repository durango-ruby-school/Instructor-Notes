# Week 4 - Models

## MVC
Rails is an MVC framework built on Ruby. MVC = Model, View Controller.

Controller (covered last week)
  
* Facilitates control logic
* Responsible to get the information from a server request and send them to a view to be rendered.
* A bridge of communication between the models and views

Views (covered last week)

* Presentation layer.
* Templates that contain html and mixed with ruby code in order to present to the user the final response.

Models 

* Facilitates business logic
* There are entities of your application that you will address using nouns, like users, tasks, etc.
* Business logic that goes around these models entail the interactions around these things, their relationships
and how they should work inside of the application.
* This includes persistence, like saving data into the database.

## Bookstore App

1. Create a new rails app

    ```
    $ cd workspaces
    
    $ rails _3.2.17_ new bookstore -T
    
    $ cd bookstore
    
    $ git add .
    
    $ git init .
    
    $ git commit -m "Initial commit"
    ```
    
    -T skips test unit
    
2. Create a book model

    ```
    $ rails generate model book name, pages:integer
    ```
3. Look at generated files
    * comment out attr_accessible for the time being
4. Migrate the database

    ```
    $ rake db:migrate
    ```
5. Show db/schema.rb
6. Check in changes

    ```
    $ git add .
    $ git commit -m "Create book model"
    ```
7. Rails console

    ```
    $ rails console
    > book = Book.new
    > book.name = "The Bible" # Didn't have to set up an attr_accessor because rails generates one for each column in db
    > book.save # Now it's saved
    
    > book_2 = Book.new name: "Charlie Brown", pages: 150
    
    # Attr Accessible Error - Fix error
    > reload!
    > book_2 = Book.new name: "Charlie Brown", pages: 150
    > book_2.save
    
    > books = Books.all
    > book = Book.find(2)
    
    > book.name = "Calvin and Hobbes"
    > book.save
    
    > book.destroy
    ```

### Taking it to the web

1. Start server

    ``` 
    $ bundle exec rails s
    ```
2. Visit /books (mention TDD flow)
3. Create books route in config/routes.rb

    ```
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
    end
    ```
4. Create app/controllers/books_controller.rb

    ```
    class BooksController < ApplicationController
    end
    ```
5. Create index method

    ```
    class BooksController < ApplicationController
      def index
      end
    end
    ```
6. Create app/views/books/index.html.erb

    ```
    <h1>Books</h1>
    ```
7. Add html table of books

    In app/views/books/index.html.erb

    ```
    <h1>Books</h1>
        
    <table>
      <tr>
        <th>Name</th>
        <th>Pages</th>
      </tr>
      <% @books.each do |book| %>
        <tr>
          <td><%= book.name %></td>
          <td><%= book.pages %></td>
        </tr>
      <% end %>
    </table>
    ```
    
    In app/controllers/books_controller.rb

    ```
    class BooksController < ApplicationController
      def index
        @books = Book.all
      end
    end
    ```
8. Create a link to the bike detail page

    In app/views/books/index.html.erb
    
    ```
    <h1>Books</h1>
        
    <table>
      <tr>
        <th>Name</th>
        <th>Pages</th>
      </tr>
      <% @books.each do |book| %>
        <tr>
          <td><%= link_to book.name, "books/#{book.id}" %></td>
          <td><%= book.pages %></td>
        </tr>
      <% end %>
    </table>
    ```
9. Create the show route

    ```
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/:id' => 'books#show'
    end
    ```
    
10. Create the show action

    ```
    class BooksController < ApplicationController
      #index method
      
      def show
        @book = Book.find params[:id]
      end
    end
    ```
11. Create app/view/books/show.html.erb

    ```
    <h1><%= @book.name %></h1>
    
    <p>Pages: <%= @book.pages %></p>
    ```

### Creating Books

1. Create a new link on the index page

    ```
    <%= link_to "New Book", "/books/new" %>
    ```
2. Create a new deck route (routes are order specific)

    ```
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id' => 'books#show'
    end
    ```
3. Create a new action in BooksController

    ```
    def new
      @book = Book.new
    end
    ```
4. Create app/views/books/new.html.erb

    ```
    <%= form_for @book do |f| %>
      <div>
        <%= f.label :name %>
        <%= f.text_field :name %>
      </div>
      <div>
        <%= f.label :pages %>
        <%= f.text_field :pages %>
      </div>
      <%= f.submit "Create Book" %>
    <% end %>
    ```
5. Create a POST create route

    ```
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id' => 'books#show'
      post '/books' => 'books#create'
    end
    ```
6. Create a create action in BooksController (show params hash in log)

    ```
    def create
      @book = Book.new params[:book]
      @book.save
      redirect_to "/books"
    end
    ```

### Editing Books

1. Add an edit link to the books page

    ```
    <h1><%= @book.name %></h1>
    
    <%= link_to "Edit", "/books/#{@book.id}/edit" %>
    
    <p>Pages: <%= @book.pages %></p>
    ```
2. Create edit route

    ```
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id/edit' => 'books#edit'
      get '/books/:id' => 'books#show'
      post '/books' => 'books#create'
    end
    ```
3. Create edit action

    ```
    def edit
      @book = Book.find params[:id]
    end
    ```
4. Create app/views/books/edit.html.erb (copy from new.html.erb)

    ```
    <%= form_for @book, url: "/books/#{@book.id}" do |f| %>
      <div>
        <%= f.label :name %>
        <%= f.text_field :name %>
      </div>
      <div>
        <%= f.label :pages %>
        <%= f.text_field :pages %>
      </div>
      <%= f.submit "Edit Book" %>
    <% end %>
    ```
    
    Need to set url since the book already exists
    
5. Add update route

    ```
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id/edit' => 'books#edit'
      get '/books/:id' => 'books#show'
      post '/books' => 'books#create'
      put '/books/:id' => 'books#update'
    end
    ```
6. Add the update action

    ```
    def update
      @book = Book.find params[:id]
      @book.update_attributes params[:book]
      redirect_to "/books/#{@book.id}"
    end
    ```

### Deleting books

1. Update app/views/pages/show.html.erb to add

    ```
    <%= link_to "Delete", "/books/#{@book.id}", method: :delete %>
    ```
2. Add delete destroy route

```
Bookstore::Application.routes.draw do
  get '/books' => 'books#index'
  get '/books/new' => 'books#new'
  get '/books/:id/edit' => 'books#edit'
  get '/books/:id' => 'books#show'
  post '/books' => 'books#create'
  put '/books/:id' => 'books#update'
  delete '/books/:id' => 'books#destroy'
end
```

3. Add destroy action

```
def destroy
  @book = Book.find(params[:id])
  @book.destroy
  redirect_to "/books"
end
```

---
### Rails directories

Take a look at directory structure created by rails new command.
One of the most important directories in your rails app is the app/ directory. You will spend the most time in this directory.
Inside this we have the following directories:

+ controllers - contains the controller files
+ views - contains templates, layouts, partials and view files
+ models - contains Active Record and model class files.
+ assets - images, javascripts, stylesheets we write for our applications.
+ helpers - classes that contain additional methods that you can add to your views
+ mailers - contain templates and methods used for sending emails from your app.

Next most important directory is the config/ directory.

+ database.yml
+ routes.rb

Other directories are

+ db/
+ log/
+ lib/ -
+ public/ - contains files that Rails does not need to handle, like a favicon image, default html index or error files.
+ doc/
+ script/
+ vendor/ - files that you did not write for the application therefore should not be altered.

#### Gemfile

```
bundle install
```

## Models
Naming syntax:

+ Model class files are singular and CamelCased - UserPhoto.
+ database is plural and use underscores - user_photos

This is important when deciding how to name your database  tables and run migrations

### Migrations
A way of versioning and recording changes to your database. It tells rails how to increment or decrement the database. It solves conflict issues very easily.

Migrations are a convenient way to alter your database schema over time in a consistent and easy way. They use a Ruby DSL so that you don't have to write SQL by hand, allowing your schema and changes to be database independent.


#### Rails generators
Programs designed to simply create files and migration syntax for you.

**rails generate migration**
Creates a file prefixed by timestamp to allow you to create new tables or add columns to existing tables.

```
rails generate migration --help
```

Migration declarations can be followed by column_name:fieldtype for as many additonal columns you would like to be added to your migration.

If the migration name is of the form "AddXXXToYYY" or "RemoveXXXFromYYY" and is followed by a list of column names and types then a migration containing the appropriate add_column and remove_column statements will be created.

```
$ rails g migration AddPartNumberToProducts part_number:string

```

will add

```
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
    add_column :products, :part_number, :string
  end
end

```
If the migration name is of the form "CreateXXX" and is followed by a list of column names and types then a migration creating the table XXX with the columns listed will be generated. For example:

```
$ rails g migration CreateProducts name:string part_number:string

```

will create a db/migrate file

```
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.string :name
      t.string :part_number
    end
  end
end

```

You can use CamelCase or underscore syntax to create your migrations

```
rails g migration add_user_id_to_products user_id:integer
is same as
rails g migration AddUserIdToProducts user_id:integer
```

**rails generate model**

```
rails generate model --help
```

This allows you to create the migration and model files needed for your rails application. Syntax is important. Ensure Model name is CamelCased and singular.

```
rails g model Product name:string description:text
```
will create the following:

+ db/migrate/YYYYMMDDHHMMSS_create_products.rb
+ app/models/product.rb  (notice singular filename)
+ test files and fixtures



### rake db:migrate
This runs the migration for the database.




