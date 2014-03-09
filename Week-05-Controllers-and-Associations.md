# Week 5 - Controllers

### Review of named routes

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
    ==  AddSummaryToBooks: migrating ==============================================
    -- add_column(:books, :summary, :text)
       -> 0.0008s
    ==  AddSummaryToBooks: migrated (0.0009s) =====================================

    ```
4. Show rollback functionality
    ```bash
    $ rake db:rollback
    ==  AddSummaryToBooks: reverting ==============================================
    -- remove_column("books", :summary)
       -> 0.0246s
    ==  AddSummaryToBooks: reverted (0.0248s) =====================================

    $ rake db:migrate
    ==  AddSummaryToBooks: migrating ==============================================
    -- add_column(:books, :summary, :text)
       -> 0.0008s
    ==  AddSummaryToBooks: migrated (0.0009s) =====================================

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

## Associations

See slides

### Adding reviews

1. Generate review model

    ```bash
    $ rails generate model review name:string rating:integer body:text book:belongs_to
    ```

2. Show migration and model
3. Migrate the database

    ```bash
    $ rake db:migrate
    ```
4. Create some reviews

    ```bash
    $ bundle exec rails c
    Loading development environment (Rails 3.2.17)
    2.0.0-p247 :001 > review = Review.new name: "Joe Smith", rating: 4, body: "Cool Cat!"
     => #<Review id: nil, name: "Joe Smith", rating: 4, body: "Cool Cat!", book_id: nil, created_at: nil, updated_at: nil>
    2.0.0-p247 :002 > review.book = Book.last
      Book Load (0.2ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" DESC LIMIT 1
     => #<Book id: 6, name: "Garfield", pages: 201, created_at: "2014-03-04 02:32:11", updated_at: "2014-03-08 17:59:01", summary: "Comic about something\r\n\r\nanother line">
    2.0.0-p247 :003 > review
     => #<Review id: nil, name: "Joe Smith", rating: 4, body: "Cool Cat!", book_id: 6, created_at: nil, updated_at: nil>
    2.0.0-p247 :004 > review.save
       (0.1ms)  begin transaction
      SQL (28.6ms)  INSERT INTO "reviews" ("body", "book_id", "created_at", "name", "rating", "updated_at") VALUES (?, ?, ?, ?, ?, ?)  [["body", "Cool Cat!"], ["book_id", 6], ["created_at", Sun, 09 Mar 2014 05:14:
    58 UTC +00:00], ["name", "Joe Smith"], ["rating", 4], ["updated_at", Sun, 09 Mar 2014 05:14:58 UTC +00:00]]
       (37.8ms)  commit transaction
     => true
    2.0.0-p247 :005 > book = Book.last
      Book Load (0.3ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" DESC LIMIT 1
     => #<Book id: 6, name: "Garfield", pages: 201, created_at: "2014-03-04 02:32:11", updated_at: "2014-03-08 17:59:01", summary: "Comic about something\r\n\r\nanother line">
    2.0.0-p247 :006 > book.reviews
    NoMethodError: undefined method `reviews' for #<Book:0x00000000dd5c20>
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activemodel-3.2.17/lib/active_model/attribute_methods.rb:407:in `method_missing'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activerecord-3.2.17/lib/active_record/attribute_methods.rb:149:in `method_missing'
            from (irb):6
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/railties-3.2.17/lib/rails/commands/console.rb:47:in `start'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/railties-3.2.17/lib/rails/commands/console.rb:8:in `start'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/railties-3.2.17/lib/rails/commands.rb:41:in `<top (required)>'
            from script/rails:6:in `require'
            from script/rails:6:in `<main>'
    2.0.0-p247 :007 >
    ```
5. Add has_many to books

    ```ruby
    class Book < ActiveRecord::Base
      has_many :reviews
      attr_accessible :name, :pages, :summary
    end
    ```
6. Back in rails console. Create a few more reviews

    ```bash
    $ bundle exec rails c
    Loading development environment (Rails 3.2.17)
    2.0.0-p247 :001 > book = Book.last
      Book Load (0.2ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" DESC LIMIT 1
     => #<Book id: 6, name: "Garfield", pages: 201, created_at: "2014-03-04 02:32:11", updated_at: "2014-03-08 17:59:01", summary: "Comic about something\r\n\r\nanother line">
    2.0.0-p247 :002 > book.reviews
      Review Load (0.2ms)  SELECT "reviews".* FROM "reviews" WHERE "reviews"."book_id" = 6
     => [#<Review id: 1, name: "Joe Smith", rating: 4, body: "Cool Cat!", book_id: 6, created_at: "2014-03-09 05:14:58", updated_at: "2014-03-09 05:14:58">]
    2.0.0-p247 :003 > review = book.reviews.new
     => #<Review id: nil, name: nil, rating: nil, body: nil, book_id: 6, created_at: nil, updated_at: nil>
    2.0.0-p247 :004 > review.attributes = {name: "Aaron Renner", rating: 5, body: "I like it!"}
     => {:name=>"Aaron Renner", :rating=>5, :body=>"I like it!"}
    2.0.0-p247 :005 > review.save
       (0.1ms)  begin transaction
      SQL (4.4ms)  INSERT INTO "reviews" ("body", "book_id", "created_at", "name", "rating", "updated_at") VALUES (?, ?, ?, ?, ?, ?)  [["body", "I like it!"], ["book_id", 6], ["created_at", Sun, 09 Mar 2014 05:24:
    38 UTC +00:00], ["name", "Aaron Renner"], ["rating", 5], ["updated_at", Sun, 09 Mar 2014 05:24:38 UTC +00:00]]
       (65.1ms)  commit transaction
     => true
    2.0.0-p247 :006 > book.reviews.count
       (0.3ms)  SELECT COUNT(*) FROM "reviews" WHERE "reviews"."book_id" = 6
     => 2
    2.0.0-p247 :007 > first_book = Book.first
      Book Load (0.3ms)  SELECT "books".* FROM "books" LIMIT 1
     => #<Book id: 5, name: "The Bible", pages: 2500, created_at: "2014-03-04 02:26:32", updated_at: "2014-03-04 02:26:32", summary: nil>
    2.0.0-p247 :008 > first_book.reviews.create name: "Aaron Renner", rating: 5, body: "Timeless and Epic.\nThe ultimate source of truth."
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "reviews" ("body", "book_id", "created_at", "name", "rating", "updated_at") VALUES (?, ?, ?, ?, ?, ?)  [["body", "Timeless and Epic.\nThe ultimate source of truth."], ["book_id", 5],
     ["created_at", Sun, 09 Mar 2014 05:26:41 UTC +00:00], ["name", "Aaron Renner"], ["rating", 5], ["updated_at", Sun, 09 Mar 2014 05:26:41 UTC +00:00]]
       (38.8ms)  commit transaction
     => #<Review id: 3, name: "Aaron Renner", rating: 5, body: "Timeless and Epic.\nThe ultimate source of truth.", book_id: 5, created_at: "2014-03-09 05:26:41", updated_at: "2014-03-09 05:26:41">
    2.0.0-p247 :009 >
    ```

7. Finding records through parents

    ```bash
    $ bundle exec rails c
    Loading development environment (Rails 3.2.17)
    2.0.0-p247 :001 > book_1 = Book.first
      Book Load (0.2ms)  SELECT "books".* FROM "books" LIMIT 1
     => #<Book id: 5, name: "The Bible", pages: 2500, created_at: "2014-03-04 02:26:32", updated_at: "2014-03-04 02:26:32", summary: nil>
    2.0.0-p247 :002 > review = book_1.reviews.first
      Review Load (0.2ms)  SELECT "reviews".* FROM "reviews" WHERE "reviews"."book_id" = 5 LIMIT 1
     => #<Review id: 3, name: "Aaron Renner", rating: 5, body: "Timeless and Epic.\nThe ultimate source of truth.", book_id: 5, created_at: "2014-03-09 05:26:41", updated_at: "2014-03-09 05:26:41">
    2.0.0-p247 :003 > book_2 = Book.last
      Book Load (0.3ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" DESC LIMIT 1
     => #<Book id: 6, name: "Garfield", pages: 201, created_at: "2014-03-04 02:32:11", updated_at: "2014-03-08 17:59:01", summary: "Comic about something\r\n\r\nanother line">
    2.0.0-p247 :004 > book_1.reviews.find(3)
      Review Load (3.2ms)  SELECT "reviews".* FROM "reviews" WHERE "reviews"."book_id" = 5 AND "reviews"."id" = ? LIMIT 1  [["id", 3]]
     => #<Review id: 3, name: "Aaron Renner", rating: 5, body: "Timeless and Epic.\nThe ultimate source of truth.", book_id: 5, created_at: "2014-03-09 05:26:41", updated_at: "2014-03-09 05:26:41">
    2.0.0-p247 :005 > book_2.reviews.find(3)
      Review Load (0.3ms)  SELECT "reviews".* FROM "reviews" WHERE "reviews"."book_id" = 6 AND "reviews"."id" = ? LIMIT 1  [["id", 3]]
    ActiveRecord::RecordNotFound: Couldn't find Review with id=3 [WHERE "reviews"."book_id" = 6]
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activerecord-3.2.17/lib/active_record/relation/finder_methods.rb:344:in `find_one'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activerecord-3.2.17/lib/active_record/relation/finder_methods.rb:315:in `find_with_ids'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activerecord-3.2.17/lib/active_record/relation/finder_methods.rb:107:in `find'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activerecord-3.2.17/lib/active_record/associations/collection_association.rb:95:in `find'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/activerecord-3.2.17/lib/active_record/associations/collection_proxy.rb:46:in `find'
            from (irb):5
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/railties-3.2.17/lib/rails/commands/console.rb:47:in `start'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/railties-3.2.17/lib/rails/commands/console.rb:8:in `start'
            from /home/action/.rvm/gems/ruby-2.0.0-p247/gems/railties-3.2.17/lib/rails/commands.rb:41:in `<top (required)>'
            from script/rails:6:in `require'
            from script/rails:6:in `<main>'
    2.0.0-p247 :006 >
    ```

### Displaying reviews

1. app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController

      # Other actions omitted

      def show
        @book = Book.find params[:id]
        @reviews = @book.reviews
      end
    ```
2. app/views/books/show.html.erb

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %> |
    <%= link_to "Delete", book_path(@book), method: :delete, data:{confirm: "Are you sure?"} %>
    <p>Pages: <%= @book.pages %></p>

    <%= simple_format @book.summary %>

    <%= link_to "All Books", books_path %>

    <h3>Reviews</h3>
    <% @reviews.each do |review| %>
      <div>
        <%= simple_format review.body %>
        <p style="font-style: italic;"><%= review.name %> gave this book a <%= review.rating %> star rating.</p>
      </div>
    <% end %>
    ```
3. app/views/books/index.html.erb

    ```
    <h1>Books</h1>

    <%= link_to "New Book", new_book_path %>

    <table>
      <tr>
        <th>Name</th>
        <th>Pages</th>
        <th>Review Count</th>
      </tr>
      <% @books.each do |book| %>
      <tr>
        <td><%= link_to book.name, book_path(book) %></td>
        <td><%= book.pages %></td>
        <td><%= book.reviews.count %></td>
      </tr>
      <% end %>
    </table>
    ```

## Nested Routes

This will allow us to visit /books/1/reviews/3

1. app/views/books/show.html.erb

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %> |
    <%= link_to "Delete", book_path(@book), method: :delete, data:{confirm: "Are you sure?"} %>
    <p>Pages: <%= @book.pages %></p>

    <%= simple_format @book.summary %>

    <%= link_to "All Books", books_path %>

    <h3>Reviews</h3>
    <% @reviews.each do |review| %>
      <div>
        <%= simple_format review.body %>
        <%= link_to [@book, review], style:"font-style: italic; display: block;" do %>
          <%= review.name %> gave this book a <%= review.rating %> star rating.
        <% end %>
      </div>
    <% end %>
    ```
2. config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      resources :books do
        resources :reviews, only: [:show]
      end
    end
    ```
3. Look at routes

    ```
    $ rake routes
    book_review GET    /books/:book_id/reviews/:id(.:format) reviews#show
          books GET    /books(.:format)                      books#index
                POST   /books(.:format)                      books#create
       new_book GET    /books/new(.:format)                  books#new
      edit_book GET    /books/:id/edit(.:format)             books#edit
           book GET    /books/:id(.:format)                  books#show
                PUT    /books/:id(.:format)                  books#update
                DELETE /books/:id(.:format)                  books#destroy
    ```

4. Create reviews controller (app/controllers/reviews_controller.rb)

    ```ruby
    class ReviewsController < ApplicationController
      def show
        @book = Book.find params[:book_id]
        @review = @book.reviews.find params[:id]
      end
    end
    ```
5. Create view (app/views/reviews/show.html.erb)

    ```
    <h1>Review #<%= @review.id %> for <%= link_to @book.name, @book %></h1>

    <p><%= @review.name %> gave this book <%= @review.rating %> stars</p>

    <%= simple_format @review.body %>
    ```
