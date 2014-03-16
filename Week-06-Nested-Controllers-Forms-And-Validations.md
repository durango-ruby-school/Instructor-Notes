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
        @review = @book.reviews.create params[:review]
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





Also, maybe talk about cascade delete
Validations,
Form Builder
