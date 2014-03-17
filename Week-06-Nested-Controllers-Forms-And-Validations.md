# Week 6 - Nested Controllers, Forms and Validations

## Nested Resources (continued)

### New Reviews

1. Add a new review link on app/views/reviews/show.html.erb

    ```
    <h1><%= @book.name %></h1>

    <%= link_to "Edit", edit_book_path(@book) %> |
    <%= link_to "Delete", @book, method: :delete, data:{confirm: "Are you sure?"} %>
    <p>Pages: <%= @book.pages %></p>
    <p><%= simple_format @book.summary %></p>

    <h2>Reviews</h2>

    <!-- Add this line --->
    <%= link_to "Add Review", new_book_review_path(@book) %>

    <% @reviews.each do |review| %>
    <div>
      <%= simple_format review.body %>
      <%= link_to [@book, review] do %>
        <p style="font-style: italic"><%= review.name %> gave this book a <%= review.rating %> star rating.</p>
      <% end %>
    </div>
    <% end %>

    <%= link_to "All Books", books_path %>
    ```

2. Add the new route in config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      resources :books do
        resources :reviews, only: [:show, :new]
      end
    end
    ```
3. Create the new action in app/controllers/reviews_controller.rb

    ```ruby
    class ReviewsController < ApplicationController
      def show
        @book = Book.find params[:book_id]
        @review = @book.reviews.find params[:id]
      end

      def new
        @book = Book.find params[:book_id]
        @review = @book.reviews.new
      end
    end
    ```
3. Create app/views/reviews/new.html.erb

    ```
    <%= form_for [@book, @review] do |f| %>
      <div>
        <%= f.label :name %>
        <%= f.text_field :name %>
      </div>
      <div>
        <%= f.label :rating %>
        <%= f.text_field :rating %>
      </div>
      <div>
        <%= f.label :body %>
        <%= f.text_area :body %>
      </div>

      <%= f.submit %>
    <% end %>
    ```
4. Add create route in config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      resources :books do
        resources :reviews, only: [:show, :new, :create]
      end
    end
    ```
5. Add the create action to app/controllers/reviews_controller.rb

    ```ruby
    class ReviewsController < ApplicationController
      def show
        @book = Book.find params[:book_id]
        @review = @book.reviews.find params[:id]
      end

      def new
        @book = Book.find params[:book_id]
        @review = @book.reviews.new
      end

      def create
        @book = Book.find params[:book_id]
        @review = @book.reviews.new params[:review]
        @review.save
        redirect_to [@book, @review]
      end
    end
    ```
6. Commit

    ```bash
    git add .
    git commit -m "New review functionality"
    ```
7. Refactor app_controllers/reviews_controller.rb to use a before_filter

    ```ruby
    class ReviewsController < ApplicationController
      before_filter :find_book

      def show
        @review = @book.reviews.find params[:id]
      end

      def new
        @review = @book.reviews.new
      end

      def create
        @review = @book.reviews.create params[:book]
        @review.save
        redirect_to [@book, @review]
      end

      private

      def find_book
        @book = Book.find params[:book_id]
      end
    end
    ```
8. Commit

    ```bash
    git add .
    git commit -m "Extract book loading to before filter"
    ```

### Editing Reviews

1. Create an edit link in app/views/reviews/show.html.erb

    ```
    <h1>Review #<%= @review.id %> for <%= link_to @book.name, @book %></h1>

    <p><%= @review.name %> gave this book <%= @review.rating %> stars</p>

    <%= simple_format @review.body %>

    <%= link_to "Edit", edit_book_review_path(@book, @review) %>
    ```
2. Add edit route to config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      resources :books do
        resources :reviews, only: [:show, :new, :create, :edit]
      end
    end
    ```
3. Add edit action to app/controllers/reviews_controller.rb

    ```ruby
    def edit
      @review = @book.reviews.find params[:id]
    end
    ```
4. Add app/views/edit.html.erb

    ```
    <%= render "form" %>
    ```
5. Move contents of app/views/reviews/new.html into app/views/reviews/_form.html.erb

    ```
    <%= form_for [@book, @review] do |f| %>
      <div>
        <%= f.label :name %>
        <%= f.text_field :name %>
      </div>
      <div>
        <%= f.label :rating %>
        <%= f.text_field :rating %>
      </div>
      <div>
        <%= f.label :body %>
        <%= f.text_area :body %>
      </div>

      <%= f.submit %>
    <% end %>
    ```
6. Edit app/views/new.html.erb

    ```
    <%= render "form" %>
    ```
7. Create update route in config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      resources :books do
        resources :reviews, only: [:show, :new, :create, :edit, :update]
      end
    end
    ```
8. Create update action in app/controllers/reviews_controller.rb

    ```ruby
    def update
      @review = @book.reviews.find params[:id]
      @review.update_attributes params[:review]
      redirect_to [@book, @review]
    end
    ```

9. Commit

    ```bash
    git add .
    git commit -m "Edit reviews"
    ```

### Deleting a review

1. Add a delete link to app/views/reviews/show.html.erb

    ```
    <h1>Review #<%= @review.id %> for <%= link_to @book.name, @book %></h1>

    <p><%= @review.name %> gave this book <%= @review.rating %> stars</p>

    <%= simple_format @review.body %>

    <%= link_to "Edit", edit_book_review_path(@book, @review) %> |
    <%= link_to "Delete", book_review_path(@book, @review), method: :delete, data: {confirm: "Are you sure?"} %>
    ```
2. Add the destroy route to config/routes.rb

    ```ruby
    Bookstore::Application.routes.draw do
      resources :books do
        resources :reviews, only: [:show, :new, :create, :edit, :update, :destroy]
      end
    end
    ```
3. Add the destroy action to app/controllers/reviews_controller.rb

    ```ruby
    def destroy
      @review = @book.reviews.find params[:id]
      @review.destroy
      redirect_to @book
    end
    ```
4. Commit

    ```bash
    git add .
    git commit -m "Delete reviews"
    ```

## Validations

1. Require book name is set. In app/models/book.rb

    ```ruby
    class Book < ActiveRecord::Base
      has_many :reviews
      attr_accessible :name, :pages, :summary

      validates_presence_of :name
    end
    ```
2. Try to create new book without a title in the console

    ```
    $ rails c
    Loading development environment (Rails 3.2.17)
    2.0.0-p247 :001 > book = Book.new
     => #<Book id: nil, name: nil, pages: nil, created_at: nil, updated_at: nil, summary: nil>
    2.0.0-p247 :002 > book.save
       (0.1ms)  begin transaction
    [deprecated] I18n.enforce_available_locales will default to true in the future. If you really want to skip validation of your locale you can set I18n.enforce_available_locales = f
    alse to avoid this message.
       (0.1ms)  rollback transaction
     => false
    2.0.0-p247 :003 > book.errors.full_messages
     => ["Name can't be blank"]
    2.0.0-p247 :004 > book.name = "War and Peace"
     => "War and Peace"
    2.0.0-p247 :005 > book.valid?
     => true
    2.0.0-p247 :006 > book.save
       (0.1ms)  begin transaction
      SQL (4.9ms)  INSERT INTO "books" ("created_at", "name", "pages", "summary", "updated_at") VALUES (?, ?, ?, ?, ?)  [["created_at", Mon, 17 Mar 2014 00:46:12 UTC +00:00], ["name",
     "War and Peace"], ["pages", nil], ["summary", nil], ["updated_at", Mon, 17 Mar 2014 00:46:12 UTC +00:00]]
       (30.5ms)  commit transaction
     => true
    2.0.0-p247 :007 >
    ```
3. Update app/controllers/books_controller.rb to re-display the form if the create action fails

    ```ruby
    def create
      @book = Book.new params[:book]
      if @book.save
        redirect_to @book
      else
        render 'new'
      end
    end
    ```

    ```ruby
    def update
      @book = Book.find params[:id]
      if @book.update_attributes params[:book]
        redirect_to @book
      else
        render "edit"
      end
    end
    ```

4. Show the errors on the book form (app/views/books/_form.html.erb)

    ```
    <%= form_for @book do |f| %>
      <% if @book.errors.any? %>
      <div class="errors">
        The form contains <%= pluralize(@book.errors.count, "error") %>.
         <ul>
        <% @book.errors.full_messages.each do |msg| %>
          <li> <%= msg %></li>
        <% end %>
        </ul>
      </div>
      <% end %>
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
5. Ensure pages greater than 0. (app/models/book.rb)

    ```ruby
    class Book < ActiveRecord::Base
      has_many :reviews
      attr_accessible :name, :pages, :summary

      validates_presence_of :name
      validates_numericality_of :pages, greater_than: 0, allow_blank: true
    end
    ```
6. Commit

    ```bash
    git add .
    git commit -m "Added book validations"
    ```

## Inline error messages and form builder

1. Add simple_form to the Gemfile.

    ```ruby
    gem 'simple_form', '~> 2.1.1'
    ```
2. Install gem

    ```bash
    bundle install
    ```
3. Run simple form install generator

    ```bash
    rails generate simple_form:install
    ```
4. Update app/views/books/_form.html.erb

    ```
    <%= simple_form_for @book do |f| %>
      <%= f.input :name %>
      <%= f.input :pages %>
      <%= f.input :summary %>

      <%= f.submit %>
    <% end %>
    ```
5. Commit

    ```bash
    git add .
    git commit -m "Added simple form"
    ```

## Adding bootstrap

1. Add the bootstrap-sass gem to the Gemfile (in the assets group)

    ```ruby
    gem 'bootstrap-sass', '~> 2.1.1.0'
    ```
2. Bundle install

    ```bash
    bundle install
    ```
3. Rename app/assets/stylesheets/application.css to app/assets/stylesheets/application.css.scss
    ```
    git mv app/assets/stylesheets/application.css app/assets/stylesheets/application.css.scss
    ```
4. Import bootstrap in app/assets/stylesheets/application.css.scss

    ```scss
    @import "bootstrap";
    ```
5. Reinstall simple form with bootstrap support

    ```bash
    rails generate simple_form:install --bootstrap
    ```
6. Style the form buttons app/views/books/_form.html.erb

    ```
    <%= simple_form_for @book do |f| %>
      <%= f.input :name %>
      <%= f.input :pages %>
      <%= f.input :summary %>

      <%= f.submit class: "btn btn-primary" %>
    <% end %>
    ```
7. Commit

    ```
    git add .
    git commit -m "Added bootstrap"
    ```

## Review validations

1. Update app/controllers/reviews_controller.rb to handle invalid data

    ```ruby
    def create
      @review = @book.reviews.new params[:review]
      if @review.save
        redirect_to [@book, @review]
      else
        render "new"
      end
    end
    ```

    ```ruby
    def update
      @review = @book.reviews.find params[:id]
      if @review.update_attributes params[:review]
        redirect_to [@book, @review]
      else
        render "edit"
      end
    end
    ```


2. Make review name, rating, and body required (app/models/review.rb)

    ```ruby
    class Review < ActiveRecord::Base
      belongs_to :book
      attr_accessible :body, :name, :rating

      validates_presence_of :name, :rating, :body
    end
    ```
3. Convert app/views/reviews/_form.html.erb to use simple form

    ```
    <%= simple_form_for [@book, @review] do |f| %>
      <%= f.input :name %>
      <%= f.input :rating %>
      <%= f.input :body %>

      <%= f.submit, class: "btn btn-primary"%>
    <% end %>
    ```
4. Commit

    ```bash
    git add .
    git commit -m "Reviews simple form and validations"
    ```

### Rating as a dropdown

1. Update review model to only allow ratings as integers between 1 and 5

    ```ruby
    class Review < ActiveRecord::Base
      belongs_to :book
      attr_accessible :body, :name, :rating

      validates_presence_of :name, :rating, :body
      validates_numericality_of :rating, only_integer: true, greater_than_or_equal_to: 1, less_than_or_equal_to: 5
    end
    ```
2. Update review form to render ratings as a select menu

    ```
    <%= simple_form_for [@book, @review] do |f| %>
      <%= f.input :name %>
      <%= f.input :rating, as: :select, collection: 1..5 %>
      <%= f.input :body %>

      <%= f.submit, class: "btn btn-primary" %>
    <% end %>
    ```
3. Commit

    ```bash
    git add .
    git commit -m "Rating is now a dropdown"
    ```

## Destroy related reviews when deleting books

1. Update app/views/book.rb

```ruby
class Book < ActiveRecord::Base
  has_many :reviews, dependent: :destroy
  attr_accessible :name, :pages, :summary

  validates_presence_of :name
  validates_numericality_of :pages, greater_than: 0, allow_blank: true
end
```
