# Week 8 - TDD and unit testing

## Flash Messages

Allows you to send messages back to user like Book was succesfully created. Renders regardless of which page you refresh to.

Two different types of messages

* :notice - success messages
* :alert - error messages

Steps

1. Update spec/features/book_management_spec.rb to test for flash message

    ```ruby
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path

        click_link "Books"
        click_link "Add book"

        fill_in "Title", with: "Garfield"
        fill_in "Pages", with: "250"

        click_button "Save"

        expect(page).to have_content "successfully created"
      end
    end
    ```

2. Run the spec

      ```bash
      $ rspec spec/features/book_management_spec.rb
      F

      Failures:

        1) Book management User manages books
           Failure/Error: expect(page).to have_content "successfully created"
             expected to find text "successfully created" in "Books Garfield"
           # ./spec/features/book_management_spec.rb:15:in `block (2 levels) in <top (required)>'

      Finished in 0.24735 seconds
      1 example, 1 failure

      Failed examples:

      rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

      Randomized with seed 36959
      ```

3. Update the create method app/controllers/books_controller.rb

    ```ruby
      def create
        @book = Book.new book_params
        if @book.save
          flash[:notice] = "Book successfully created."
          redirect_to @book
        else
          render "new"
        end
      end
    ```

4. Update the app/views/layouts/application.html.erb to render flash mesages

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
      <%= link_to "Books", books_path %>

      <% flash.each do |name, msg| %>
      <div class="flash <%= name %>"><%= msg %></div>
      <% end %>

    <%= yield %>

    </body>
    </html>
    ```

5. Run the specs

    ```
    $ rspec spec/features/book_management_spec.rb
    .

    Finished in 0.76564 seconds
    1 example, 0 failures

    Randomized with seed 61984
    ```

6. Commit

    ```
    $ git add .
    $ git commit -m "Added flash messages"
    ```

6. Fire up the server and check it out


## Edit and Delete

1. Update spec to include edit and delete

    ```
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path

        click_link "Books"
        click_link "Add book"

        fill_in "Title", with: "Garfield"
        fill_in "Pages", with: "250"

        click_button "Save"

        expect(page).to have_content "successfully created"

        click_link "Edit"
        fill_in "Title", with: "Calvin and Hobbes"
        click_button "Save"

        expect(page).to have_content "successfully updated"
        expect(page).to have_content "Calvin and Hobbes"

        click_link "Delete"
        expect(page).to have_content "successfully deleted"
      end
    end
    ```

2. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Edit"
         Capybara::ElementNotFound:
           Unable to find link "Edit"
         # ./spec/features/book_management_spec.rb:17:in `block (2 levels) in <top (required)>'

    Finished in 0.24665 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 48927
    ```

3. Add edit link in app/views/books/show.html.erb

    ```
    <%= link_to "Edit", edit_book_path(@book) %>

    <h1><%= @book.title %></h1>
    ```

4. Run spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActionView::Template::Error:
           undefined method `edit_book_path' for #<#<Class:0x00000003aa1da8>:0x0000000481d090>
         # ./app/views/books/show.html.erb:1:in `_app_views_books_show_html_erb__4335723529987197110_37835000'
         # ./spec/features/book_management_spec.rb:13:in `block (2 levels) in <top (required)>'

    Finished in 0.7835 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 14255
    ```

5. Update config/routes.rb

    ```
    Readathon::Application.routes.draw do
      root "pages#index"

      resources :books, only: [:index, :new, :create, :show, :edit]
    end
    ```

6. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Edit"
         AbstractController::ActionNotFound:
           The action 'edit' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:17:in `block (2 levels) in <top (required)>'

    Finished in 0.26976 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 59630
    ```

7. Add the edit action to the books controller

    ```ruby
    class BooksController < ApplicationController
      def index
      end

      def new
        @book = Book.new
      end

      def create
        @book = Book.new book_params
        if @book.save
          flash[:notice] = "Book successfully created."
          redirect_to @book
        else
          render "new"
        end
      end

      def show
        @book = Book.find params[:id]
      end

      def edit
        @book = Book.find params[:id]
      end

      private

      def book_params
        params.require(:book).permit(:title, :pages)
      end
    end
    ```

8. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Edit"
         ActionView::MissingTemplate:
           Missing template books/edit, application/edit with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :coffee, :jbuilder]}. Searched in:
             * "/home/action/workspace/readathon/app/views"
         # ./spec/features/book_management_spec.rb:17:in `block (2 levels) in <top (required)>'

    Finished in 0.76224 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 46924
    ```

9. Create app/views/books/edit.html.erb

    ```
    <%= simple_form_for @book do |f| %>
      <%= f.input :title %>
      <%= f.input :pages %>

      <%= f.submit "Save" %>
    <% end %>
    ```

10. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         ActionController::RoutingError:
           No route matches [PATCH] "/books/8"
         # ./spec/features/book_management_spec.rb:19:in `block (2 levels) in <top (required)>'

    Finished in 0.76454 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 13097
    ```

11. Add the update route

    ```ruby
    Readathon::Application.routes.draw do
      root "pages#index"

      resources :books, only: [:index, :new, :create, :show, :edit, :update]
    end
    ```

12. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         AbstractController::ActionNotFound:
           The action 'update' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:19:in `block (2 levels) in <top (required)>'

    Finished in 0.29706 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 61146
    ```

13. Create update action in app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController
      # Other actions omitted

      def update
        @book = Book.find params[:id]
        if @book.update_attributes book_params
          flash[:notice] = "Book successfully updated."
          redirect_to @book
        else
          render "edit"
        end
      end
    end
    ```

14. Run the spec

    ```ruby
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Delete"
         Capybara::ElementNotFound:
           Unable to find link "Delete"
         # ./spec/features/book_management_spec.rb:24:in `block (2 levels) in <top (required)>'

    Finished in 0.79845 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 15875
    ```

15. Add a delete link to app/views/books/show.html.erb

    ```
    <%= link_to "Edit", edit_book_path(@book) %> |
    <%= link_to "Delete", @book, method: :delete %>

    <h1><%= @book.title %></h1>
    ```

16. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Delete"
         ActionController::RoutingError:
           No route matches [DELETE] "/books/15"
         # ./spec/features/book_management_spec.rb:24:in `block (2 levels) in <top (required)>'

    Finished in 0.78999 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 23182
    ```

17. Add the destroy route

    ```
    Readathon::Application.routes.draw do
      root "pages#index"

      resources :books, only: [:index, :new, :create, :show, :edit, :update, :destroy]
    end
    ```

18. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Delete"
         AbstractController::ActionNotFound:
           The action 'destroy' could not be found for BooksController
         # ./spec/features/book_management_spec.rb:24:in `block (2 levels) in <top (required)>'

    Finished in 0.3065 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 54552
    ```

19. Add the destroy action

    ```ruby
    class BooksController < ApplicationController
      # Other actions omitted...

      def destroy
        @book = Book.find params[:id]
        if @book.destroy
          flash[:notice] = "Book successfully deleted."
        end
        redirect_to books_path
      end
    end
    ```

20. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    .

    Finished in 0.80757 seconds
    1 example, 0 failures

    Randomized with seed 3856
    ```

21. Commit

    ```bash
    $ git add .
    $ git commit -m "Finished book crud"
    ```


## Refactoring

1. Let's start by making the spec more readable. spec/features/book_management_spec.rb

    ```ruby
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path

        click_link "Books"
        click_link "Add book"

        fill_in "Title", with: "Garfield"
        fill_in "Pages", with: "250"

        click_button "Save"

        user_sees_flash_message "successfully created"

        click_link "Edit"
        fill_in "Title", with: "Calvin and Hobbes"
        click_button "Save"

        user_sees_flash_message "successfully updated"
        expect(page).to have_content "Calvin and Hobbes"

        click_link "Delete"
        user_sees_flash_message "successfully deleted"
      end

      def user_sees_flash_message msg
        expect(page).to have_content msg
      end
    end
    ```

2. Run the spec and make sure things still work

    ```
    $ rspec spec/features/book_management_spec.rb
    .

    Finished in 0.81859 seconds
    1 example, 0 failures

    Randomized with seed 14977
    ```

3. Extract button saving

    ```
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path

        click_link "Books"
        click_link "Add book"

        fill_in "Title", with: "Garfield"
        fill_in "Pages", with: "250"
        save

        user_sees_flash_message "successfully created"

        click_link "Edit"
        fill_in "Title", with: "Calvin and Hobbes"
        save

        user_sees_flash_message "successfully updated"
        expect(page).to have_content "Calvin and Hobbes"

        click_link "Delete"
        user_sees_flash_message "successfully deleted"
      end

      def user_sees_flash_message msg
        expect(page).to have_content msg
      end

      def save
        click_button "Save"
      end
    end
    ```

4. Run the spec and make sure things still work

    ```
    $ rspec spec/features/book_management_spec.rb
    .

    Finished in 0.80727 seconds
    1 example, 0 failures

    Randomized with seed 37814
    ```

5. Extract book adding

    ```ruby
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path
        click_link "Books"

        add_book_with_title "Garfield"

        user_sees_flash_message "successfully created"

        click_link "Edit"
        fill_in "Title", with: "Calvin and Hobbes"
        save

        user_sees_flash_message "successfully updated"
        expect(page).to have_content "Calvin and Hobbes"

        click_link "Delete"
        user_sees_flash_message "successfully deleted"
      end

      def add_book_with_title title
        click_link "Add book"

        fill_in "Title", with: title
        fill_in "Pages", with: "250"
        save
      end

      def user_sees_flash_message msg
        expect(page).to have_content msg
      end

      def save
        click_button "Save"
      end
    end
    ```

6. Run the spec and make sure it still works
7. Extract editing book title

    ```ruby
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path
        click_link "Books"

        add_book_with_title "Garfield"

        user_sees_flash_message "successfully created"

        edit_book_title "Calvin and Hobbes"

        user_sees_flash_message "successfully updated"
        expect(page).to have_content "Calvin and Hobbes"

        click_link "Delete"
        user_sees_flash_message "successfully deleted"
      end

      def add_book_with_title title
        click_link "Add book"

        fill_in "Title", with: title
        fill_in "Pages", with: "250"
        save
      end

      def edit_book_title title
        click_link "Edit"
        fill_in "Title", with: title
        save
      end

      def user_sees_flash_message msg
        expect(page).to have_content msg
      end

      def save
        click_button "Save"
      end
    end
    ```

8. Run the spec and make sure things still work
9. Extract the book title to helper

    ```ruby
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path
        click_link "Books"

        add_book_with_title "Garfield"

        user_sees_flash_message "successfully created"

        edit_book_title "Calvin and Hobbes"

        user_sees_flash_message "successfully updated"
        user_sees_book_title "Calvin and Hobbes"

        click_link "Delete"
        user_sees_flash_message "successfully deleted"
      end

      def add_book_with_title title
        click_link "Add book"

        fill_in "Title", with: title
        fill_in "Pages", with: "250"
        save
      end

      def edit_book_title title
        click_link "Edit"
        fill_in "Title", with: title
        save
      end

      def user_sees_book_title title
        expect(page).to have_content title
      end

      def user_sees_flash_message msg
        expect(page).to have_content msg
      end

      def save
        click_button "Save"
      end
    end
    ```

10. Run the spec to make sure it still passes
11. Commit

    ```
    $ git add .
    $ git commit -m "Refactored test to help readability"
    ```

12. Extract book loading into before filter in app/controllers/books_controller.rb

    ```ruby
    class BooksController < ApplicationController
      before_filter :find_book, only: [ :show, :edit, :update, :destroy]

      def index
      end

      def new
        @book = Book.new
      end

      def create
        @book = Book.new book_params
        if @book.save
          flash[:notice] = "Book successfully created."
          redirect_to @book
        else
          render "new"
        end
      end

      def show
      end

      def edit
      end

      def update
        if @book.update_attributes book_params
          flash[:notice] = "Book successfully updated."
          redirect_to @book
        else
          render "edit"
        end
      end

      def destroy
        if @book.destroy
          flash[:notice] = "Book successfully deleted."
        end
        redirect_to books_path
      end


      private

      def find_book
        @book = Book.find params[:id]
      end

      def book_params
        params.require(:book).permit(:title, :pages)
      end
    end
    ```

13. Run your spec and make sure it still works.
14. Commit

    ```bash
    $ git add .
    $ git commit -m "Extracted out book loading into before filter"
    ```

15. Extract new and edit html into app/views/books/_form.html.erb

    ```
    <%= simple_form_for @book do |f| %>
      <%= f.input :title %>
      <%= f.input :pages %>

      <%= f.submit "Save" %>
    <% end %>
    ```

16. Update app/views/books/edit.html.erb and app/views/books/new.html.erb to use the partial

    ```
    <%= render partial: "form" %>
    ```

17. Run the spec and make sure it still works
18. Commit

    ```
    $ git add .
    $ git commit -m "Extracted book form into partial"
    ```

## Matching elements with CSS selectors

1. Update the form so you don't explicitly name the submit button "Save"

    ```
    <%= simple_form_for @book do |f| %>
      <%= f.input :title %>
      <%= f.input :pages %>

      <%= f.submit %>
    <% end %>
    ```

2. Run the spec

    ```
    Failures:

      1) Book management User manages books
         Failure/Error: click_button "Save"
         Capybara::ElementNotFound:
           Unable to find button "Save"
         # ./spec/features/book_management_spec.rb:44:in `save'
         # ./spec/features/book_management_spec.rb:26:in `add_book_with_title'
         # ./spec/features/book_management_spec.rb:8:in `block (2 levels) in <top (required)>'

    Finished in 0.73467 seconds
    1 example, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books

    Randomized with seed 40644
    ```

3. Start up the server and see what else you could reference the submit button by
4. Modify your test to use the css selector "input[type=submit]"

    ```ruby
    require "spec_helper"

    feature "Book management" do
      scenario "User manages books" do
        visit root_path
        click_link "Books"

        add_book_with_title "Garfield"

        user_sees_flash_message "successfully created"

        edit_book_title "Calvin and Hobbes"

        user_sees_flash_message "successfully updated"
        user_sees_book_title "Calvin and Hobbes"

        click_link "Delete"
        user_sees_flash_message "successfully deleted"
      end

      def add_book_with_title title
        click_link "Add book"

        fill_in "Title", with: title
        fill_in "Pages", with: "250"
        save
      end

      def edit_book_title title
        click_link "Edit"
        fill_in "Title", with: title
        save
      end

      def user_sees_book_title title
        expect(page).to have_content title
      end

      def user_sees_flash_message msg
        expect(page).to have_content msg
      end

      def save
        find("input[type=submit]").click
      end
    end
    ```

5. Run the spec to make sure it still works
6. Commit

    ```
    $ git add .
    $ git commit -m "Changed submit buttons to use default text"
    ```

7. Update config/routes.rb to just use the resources call, since we are defining all 7 routes

    ```
    Readathon::Application.routes.draw do
      root "pages#index"

      resources :books
    end
    ```

8. Run rake and make sure everything still works
9. Commit

    ```
    $ git add .
    $ git commit -m "Cleaned up routes"
    ```

## Testing required fields

1. Create another scenario in spec/features/book_management_spec.rb

    ```ruby
      scenario 'user tries to save invalid book' do
        visit new_book_path

        fill_in "Pages", with: "200"
        save

        expect(page).to_not have_content "successfully created"

        fill_in "Title", with: "Star Wars"
        save

        user_sees_flash_message "successfully created"
      end
    ```

2. Run the spec

    ```bash
    $ rspec spec/features/book_management_spec.rb
    .F

    Failures:

      1) Book management user tries to save invalid book
         Failure/Error: expect(page).to_not have_content "successfully created"
           expected not to find text "successfully created" in "Books Book successfully created. Edit | Delete"
         # ./spec/features/book_management_spec.rb:28:in `block (2 levels) in <top (required)>'

    Finished in 0.87525 seconds
    2 examples, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:21 # Book management user tries to save invalid book

    Randomized with seed 8897
    ```

3. Let's create a unit test to just test the user validation. spec/models/user_spec.rb

    ```
    require 'spec_helper'

    describe Book, "validations" do
      it "is valid when it has a title attribute" do
        book = Book.new title: "Something"
        expect(book.valid?).to be_true
      end

       it "is not valid when it is missing a title attribute" do
        book = Book.new
        expect(book.valid?).to be_false
      end
    end
    ```

4. Run your new spec

    ```
    $ rspec spec/models/book_spec.rb
    .F

    Failures:

      1) Book validations is not valid when it is missing a title attribute
         Failure/Error: expect(book.valid?).to be_false
           expected: false value
                got: true
         # ./spec/models/book_spec.rb:11:in `block (2 levels) in <top (required)>'

    Finished in 0.01662 seconds
    2 examples, 1 failure

    Failed examples:

    rspec ./spec/models/book_spec.rb:9 # Book validations is not valid when is missing a title attribute

    Randomized with seed 16767
    ```

5. Add validates_presence_of to your book model

    ```ruby
    class Book < ActiveRecord::Base
      validates_presence_of :title
    end
    ```

6. Run your spec

    ```
    $ rspec spec/models/book_spec.rb
    ..

    Finished in 0.02011 seconds
    2 examples, 0 failures

    Randomized with seed 1631
    ```

7. Now add tests to make sure pages are required

    ```
    require 'spec_helper'

    describe Book, "validations" do
      it "is valid when it has a title attribute" do
        book = Book.new title: "Something"
        expect(book.valid?).to be_true
      end

       it "is not valid when it is missing a title attribute" do
        book = Book.new
        expect(book.valid?).to be_false
      end

      it "is valid when it has a pages attribute" do
        book = Book.new pages: "Something"
        expect(book.valid?).to be_true
      end

      it "is not valid when it is missing a pages attribute" do
        book = Book.new
        expect(book.valid?).to be_false
      end
    end
    ```

8. Run the spec

    ```
    $ rspec spec/models/book_spec.rb
    .F..

    Failures:

      1) Book validations is valid when it has a pages attribute
         Failure/Error: expect(book.valid?).to be_true
           expected: true value
                got: false
         # ./spec/models/book_spec.rb:16:in `block (2 levels) in <top (required)>'

    Finished in 0.02594 seconds
    4 examples, 1 failure

    Failed examples:

    rspec ./spec/models/book_spec.rb:14 # Book validations is valid when it has a pages attribute

    Randomized with seed 402
    ```

9. Make pages required.

    ```
    class Book < ActiveRecord::Base
      validates_presence_of :title, :pages
    end
    ```

10. Run the spec.

    ```
    $ rspec spec/models/book_spec.rb
    F.F.

    Failures:

      1) Book validations is valid when it has a pages attribute
         Failure/Error: expect(book.valid?).to be_true
           expected: true value
                got: false
         # ./spec/models/book_spec.rb:16:in `block (2 levels) in <top (required)>'

      2) Book validations is valid when it has a title attribute
         Failure/Error: expect(book.valid?).to be_true
           expected: true value
                got: false
         # ./spec/models/book_spec.rb:6:in `block (2 levels) in <top (required)>'

    Finished in 0.06586 seconds
    4 examples, 2 failures

    Failed examples:

    rspec ./spec/models/book_spec.rb:14 # Book validations is valid when it has a pages attribute
    rspec ./spec/models/book_spec.rb:4 # Book validations is valid when it has a title attribute

    Randomized with seed 4768
    ```

11. What just happened? How can we fix this?

12. Switch to test by which fields have errors

    ```ruby
    require 'spec_helper'

    describe Book, "validations" do
      it "is valid when it has a title attribute" do
        book = Book.new title: "Something"
        book.valid?
        expect(book.errors[:title].empty?).to be_true
      end

       it "is not valid when it is missing a title attribute" do
        book = Book.new
        book.valid?
        expect(book.errors[:title].empty?).to_not be_true
      end

      it "is valid when it has a pages attribute" do
        book = Book.new pages: "Something"
        book.valid?
        expect(book.errors[:pages]).to be_empty
      end

      it "is not valid when it is missing a pages attribute" do
        book = Book.new
        book.valid?
        expect(book.errors[:pages]).to_not be_empty
      end
    end
    ```

13. Run the spec

    ```
    $ rspec spec/models/book_spec.rb
    ....

    Finished in 0.06578 seconds
    4 examples, 0 failures

    Randomized with seed 53245
    ```

14. Comment out the validates_presence_of line in app/models/book.rb and make sure it fails

15. Comment that line back in and run the tests again and make sure it passes

16. Run rake and see if all of the tests pass

    ```
    $ rake
    /home/action/.rvm/rubies/ruby-2.0.0-p247/bin/ruby -S rspec ./spec/features/book_management_spec.rb ./spec/features/view_homepage_spec.rb ./spec/models/book_spec.rb
    .......

    Finished in 0.92894 seconds
    7 examples, 0 failures

    Randomized with seed 7570
    ```

17. Commit

    ```
    $ git add .
    $ git commit -m "Made book title and pages required"
    ```

## Refactoring unit tests

Let's clean up the duplication between tests in spec/models/book_spec.rb

1. Install shoulda-matchers in your test group of your Gemfile.

    ```
    group :test do
      gem 'shoulda-matchers'
    end
    ```

2. Bundle install

3. Shoulda matchers allows you to change your tests in spec/models/book_spec.rb to read like this

    ```ruby
    require 'spec_helper'

    describe Book, "validations" do
      it{should validate_presence_of(:title)}
      it{should validate_presence_of(:pages)}
    end
    ```

4. Run your specs

    ```
    $ rspec spec/models/book_spec.rb
    ..

    Finished in 0.03258 seconds
    2 examples, 0 failures

    Randomized with seed 54297
    ```

5. Comment out your validates_presence_of lines in app/models/book.rb, run the tests and make sure it fails

6. Comment back in those lines and run rake, to make sure everything still passes

7. Commit

    ```
    $ git add .
    $ git commit -m "Switched book specs to use shoulda-matchers"
    ```



## Testing the book index page

1. Create another scenario in spec/features/book_management_spec.rb

    ```
    scenario "user looks for all books" do
      # Create a few books
      # Look at the page
      # Make sure the books show up
    end
    ```

2. Add factory girl rails to your gemfile in the development,test group

    ```ruby
    # Other gems omitted

    group :development, :test do
      gem 'rspec-rails'
      gem 'capybara'
      gem 'factory_girl_rails'
    end
    ```

3. Bundle install
4. Create your book factory spec/factories/books.rb

    ```
    FactoryGirl.define do
      factory :book do
        title "My Book"
        pages 250
      end
    end
    ```

5. Update the your spec to create some books and make sure they show up

    ```
     scenario "user looks for all books" do
        # Create a few books
        FactoryGirl.create_list :book, 3
        # Look at the page
        visit books_path
        # Make sure the books show up
        expect(page).to have_css ".book", count: 3
      end
    ```

6. Run your spec

    ```
    $ rspec spec/features/book_management_spec.rb
    F..

    Failures:

      1) Book management user looks for all books
         Failure/Error: expect(page).to have_css ".book", count: 3
         Capybara::ExpectationNotMet:
           expected to find css ".book" 3 times but there were no matches
         # ./spec/features/book_management_spec.rb:41:in `block (2 levels) in <top (required)>'

    Finished in 0.90766 seconds
    3 examples, 1 failure

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:35 # Book management user looks for all books

    Randomized with seed 34425
    ```

7. Update app/views/books/index.html

    ```
    <% @books.each do |book| %>
    <div class="book">
      <%= book.title %>
    </div>
    <% end %>
    ```

8. Run the spec

    ```

    Failures:

      1) Book management User manages books
         Failure/Error: click_link "Books"
         ActionView::Template::Error:
           undefined method `each' for nil:NilClass
         # ./app/views/books/index.html.erb:3:in `_app_views_books_index_html_erb___3101251044140856188_22759520'
         # ./spec/features/book_management_spec.rb:6:in `block (2 levels) in <top (required)>'

      2) Book management user looks for all books
         Failure/Error: visit books_path
         ActionView::Template::Error:
           undefined method `each' for nil:NilClass
         # ./app/views/books/index.html.erb:3:in `_app_views_books_index_html_erb___3101251044140856188_22759520'
         # ./spec/features/book_management_spec.rb:39:in `block (2 levels) in <top (required)>'

    Finished in 0.80397 seconds
    3 examples, 2 failures

    Failed examples:

    rspec ./spec/features/book_management_spec.rb:4 # Book management User manages books
    rspec ./spec/features/book_management_spec.rb:35 # Book management user looks for all books

    Randomized with seed 19423
    ```

9. Update the controller to assign the @books variable

    ```
    class BooksController < ApplicationController
      before_filter :find_book, only: [ :show, :edit, :update, :destroy]

      def index
        @books = Book.all
      end

    # Other actions are omitted
    end
    ```

10. Run the spec

    ```
    $ rspec spec/features/book_management_spec.rb
    ...

    Finished in 0.91608 seconds
    3 examples, 0 failures

    Randomized with seed 1596
    ```

11. Commit

    ```
    $ git add .
    $ git commit -m "Book index page"
    ```

12. Refactor app/views/books/index.html.erb to use div_for helper

    ```
    <%= link_to "Add book", new_book_path %>

    <% @books.each do |book| %>
      <%= div_for book do %>
        <%= book.title %>
      <% end %>
    <% end %>
    ```

13. Run specs to make sure it still passes
14. Commit

    ```
    git add .
    git commit -m "Refactored to use div_for"
    ```
