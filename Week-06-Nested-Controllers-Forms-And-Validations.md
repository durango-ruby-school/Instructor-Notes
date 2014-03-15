# Week 6 - Nested Controllers, Forms and Validations

## Nested forms

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
        @review = @book.reviews.create params[:book]
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
      before_filter :load_book

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

      def load_book
        @book = Book.find params[:book_id]
      end
    end
    ```
8. Commit

    ```bash
    git add .
    git commit -m "Extract book loading to before filter"
    ```
