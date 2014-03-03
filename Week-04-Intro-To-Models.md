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

    ```bash
    $ cd workspaces

    $ rails _3.2.17_ new bookstore -T

    $ cd bookstore

    $ git add .

    $ git init .

    $ git commit -m "Initial commit"
    ```

    -T skips test unit

2. Create a book model

    ```bash
    $ rails generate model book name, pages:integer
    ```
3. Look at generated files
    * comment out attr_accessible for the time being
4. Migrate the database

    ```bash
    $ rake db:migrate
    ```
5. Show db/schema.rb
6. Check in changes

    ```bash
    $ git add .
    $ git commit -m "Create book model"
    ```
7. Rails console

    ```bash
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

    ```bash
    $ bundle exec rails s
    ```
2. Visit /books (mention TDD flow)
3. Create books route in config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
    end
    ```
4. Create app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController
    end
    ```
5. Create index method

    ```ruby
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

    ```ruby
    class BooksController < ApplicationController
      def index
        @books = Book.all
      end
    end
    ```
8. Create a link to the bike detail page

    In app/views/books/index.html.erb

    ```html
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

    ```ruby
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/:id' => 'books#show'
    end
    ```

10. Create the show action

    ```ruby
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

12. Git commit

    ```bash
    $ git add .
    $ git commit -m "Displaying books"
    ```

### Creating Books

1. Create a new link on the index page

    ```
    <%= link_to "New Book", "/books/new" %>
    ```
2. Create a new deck route (routes are order specific)

    ```ruby
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id' => 'books#show'
    end
    ```
3. Create a new action in BooksController

    ```ruby
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
      <%= f.submit %>
    <% end %>
    ```
5. Create a POST create route

    ```ruby
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id' => 'books#show'
      post '/books' => 'books#create'
    end
    ```
6. Create a create action in BooksController (show params hash in log)

    ```ruby
    def create
      @book = Book.new params[:book]
      @book.save
      redirect_to "/books"
    end
    ```

7. Git Commit

    ```bash
    $ git add .
    $ git commit -m "Add books action"
    ```

### Editing Books

1. Add an edit link to the books page

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", "/books/#{@book.id}/edit" %>

    <p>Pages: <%= @book.pages %></p>
    ```
2. Create edit route

    ```ruby
    Bookstore::Application.routes.draw do
      get '/books' => 'books#index'
      get '/books/new' => 'books#new'
      get '/books/:id/edit' => 'books#edit'
      get '/books/:id' => 'books#show'
      post '/books' => 'books#create'
    end
    ```
3. Create edit action

    ```ruby
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
      <%= f.submit %>
    <% end %>
    ```

    Need to set url since the book already exists

5. Add update route

    ```ruby
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

    ```ruby
    def update
      @book = Book.find params[:id]
      @book.update_attributes params[:book]
      redirect_to "/books/#{@book.id}"
    end
    ```
7. Git commit

    ```bash
    $ git add .
    $ git commit -m "Update books action"
    ```

### Deleting books

1. Update app/views/pages/show.html.erb to add

    ```ruby
    <%= link_to "Delete", "/books/#{@book.id}", method: :delete %>
    ```
2. Add delete destroy route

    ```ruby
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

    ```ruby
    def destroy
      @book = Book.find(params[:id])
      @book.destroy
      redirect_to "/books"
    end
    ```

4. Git commit

    ```bash
    $ git add .
    $ git commit -m "Destroy books action"
    ```

## Cleaning Up

### Resource Routes

config/routes.rb can be changed to

```ruby
Bookstore::Application.routes.draw do
  resources :books
end
```

### Path Helpers

```bash
$ rake routes

    books GET    /books(.:format)          books#index
          POST   /books(.:format)          books#create
 new_book GET    /books/new(.:format)      books#new
edit_book GET    /books/:id/edit(.:format) books#edit
     book GET    /books/:id(.:format)      books#show
          PUT    /books/:id(.:format)      books#update
          DELETE /books/:id(.:format)      books#destroy

```

First column is the name of the path helper. Can be accessed like `new_book_path` or `new_book_url`.

* Use path unless you need the absolute url.
* Path helpers are automatically set up by `resources :books`

#### Updates to use path helpers

1. app/views/books/index.html

    ```
    <h1>Books</h1>

    <%= link_to "New Book", new_book_path %>

    <table>
      <tr>
        <th>Name</th>
        <th>Pages</th>
      </tr>
      <% @books.each do |book| %>
        <tr>
          <td><%= link_to book.name, book_path(book) %></td>
          <td><%= book.pages %></td>
        </tr>
      <% end %>
    </table>
    ```
2. app/views/books/show.html.erb

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %>
    <%= link_to "Delete", book_path(@book), method: :delete %>

    <p>Pages: <%= @book.pages %></p>

    <!-- New link -->
    <%= link_to "Back to all books", books_path %>
    ```

3. app/views/books/edit.html.erb

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
      <%= f.submit "Edit Book" %>
    <% end %>
    ```

4. app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController
      def index
        @books = Book.all
      end

      def show
        @book = Book.find params[:id]
      end

      def new
        @book = Book.new
      end

      def create
        @book = Book.new params[:book]
        @book.save
        redirect_to books_path # Changed line
      end

      def edit
        @book = Book.find params[:id]
      end

      def update
        @book = Book.find params[:id]
        @book.update_attributes params[:book]
        redirect_to book_path(@book) # Changed line
      end

      def destroy
        @book = Book.find params[:id]
        @book.destroy
        redirect_to books_path # Changed line
      end
    end
    ```

5. git commit

    ```bash
    $ git add .
    $ git commit -m "Books resource route and path helpers"
    ```

### Extracting a form partial

1. Show duplication between app/views/books/new.html.erb and app/views/books/edit.html.erb
2. Create app/views/books/_form.html.erb

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
      <%= f.submit %>
    <% end %>
    ```
3. Update app/views/books/new.html.erb and app/views/books/edit.html.erb

    ```
    <%= render partial: "form" %>
    ```
4. Git commit

    ```bash
    $ git add .
    $ git commit -m "Extract book form partial"
    ```


### Adding a Book summary field (Alexii)

1. Create a migration

    ```bash
    $ rails generate migration add_summary_to_books summary:text
    ```

2. Show migration file. Talk about why text instead of string
3. Migrate the database

    ```bash
    $ rake db:migrate
    ```
4. Restart the server `bundle exec rails s`
5. Update app/views/books/_form.html.erb

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
      <div>
        <%= f.label :summary %>
        <%= f.text_area :summary %>
      </div>
      <%= f.submit %>
    <% end %>
    ```
6. Try to submit, see Mass assignment security error
7. Update app/models/book.rb

    ```ruby
    class Book < ActiveRecord::Base
      attr_accessible :name, :pages, :summary
    end
    ```
8. Render summary on app/models/show.html.erb

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %>
    <%= link_to "Delete", book_path(@book), method: :delete %>

    <p>Pages: <%= @book.pages %></p>

    <%= @book.summary %>

    <%= link_to "Back to all books", books_path %>
    ```

9. Use simple_format view helper to include line breaks

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %>
    <%= link_to "Delete", book_path(@book), method: :delete %>

    <p>Pages: <%= @book.pages %></p>

    <p><%= simple_format @book.summary %></p>

    <%= link_to "Back to all books", books_path %>
    ```
