# Week 5 - Controllers

## Review named routes

```bash
$ rake routes
    books GET    /books(.:format)          books#index
          POST   /books(.:format)          books#create
 new_book GET    /books/new(.:format)      books#new
edit_book GET    /books/:id/edit(.:format) books#edit
     book GET    /books/:id(.:format)      books#show
          PUT    /books/:id(.:format)      books#update
          DELETE /books/:id(.:format)      books#destroy
$ bundle exec rails c
Loading development environment (Rails 3.2.17)
2.0.0-p247 :001 > app.books_path
 => "/books"
2.0.0-p247 :002 > book = Book.first
  Book Load (0.2ms)  SELECT "books".* FROM "books" LIMIT 1
 => #<Book id: 5, name: "The Bible", pages: 2500, created_at: "2014-03-04 02:26:32", updated_at: "2014-03-04 02:26:32">
2.0.0-p247 :003 > app.book_path(book)
 => "/books/5"
2.0.0-p247 :004 > app.edit_book_path(book)
 => "/books/5/edit"
2.0.0-p247 :005 > helper.link_to "Edit", app.edit_book_path(book)
 => "<a href=\"/books/5/edit\">Edit</a>"
2.0.0-p247 :005 > helper.link_to "Show", app.book_path(book)
 => "<a href=\"/books/5\">Show</a>"
2.0.0-p247 :005 > app.polymorphic_path(book)
 => "/books/5"
2.0.0-p247 :006 > exit
```

Anywhere you use book_path, you can just pass in the object

  * app/controllers/books_controller.rb

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
          redirect_to @book #Changed
        end

        def edit
          @book = Book.find params[:id]
        end

        def update
          @book = Book.find params[:id]
          @book.update_attributes params[:book]
          redirect_to @book # Changed
        end

        def destroy
          @book = Book.find params[:id]
          @book.destroy
          redirect_to books_path
        end
      end
      ```
  * app/views/index.html.erb

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
          <!-- changed -->
          <td><%= link_to book.name, book %></td>
          <td><%= book.pages %></td>
        </tr>
        <% end %>
      </table>
      ```
  * app/views/show.html.erb

      ```
      <h1><%= @book.name %></h1>

      <%= link_to "Edit", edit_book_path(@book) %> |
      <!-- Changed book_path(@book) to @book -->
      <%= link_to "Delete", @book, method: :delete, data:{confirm: "Are you sure?"} %>
      <p>Pages: <%= @book.pages %></p>

      <%= link_to "All Books", books_path %>
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
    <%= link_to "Delete", @book, method: :delete %>

    <p>Pages: <%= @book.pages %></p>

    <%= @book.summary %>

    <%= link_to "Back to all books", books_path %>
    ```

9. Use simple_format view helper to include line breaks

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %>
    <%= link_to "Delete", @book, method: :delete %>

    <p>Pages: <%= @book.pages %></p>

    <p><%= simple_format @book.summary %></p>

    <%= link_to "Back to all books", books_path %>
    ```
