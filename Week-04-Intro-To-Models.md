# Week 4 - Models and Deploying to Heroku

## Setting up Postgres
http://help.nitrous.io/postgresql/

Also available from the AutoParts menu

```
$ parts install postgresql

$ parts start postgresql
```

#### Install rails
From command-line install correct version of rails

```
gem install rails -v 3.2.17
```
 
#### Create rails app and set postgres as default database
from command-line

```
$ rails _3.2.17_ new my_site -d postgresql
```

#### Rails is MVC 
Rails is an MVC framework built on Ruby. MVC = Model, View Controller.

```
Controller - Facilitates control logic
Responsible to get the information from a server request and send them to a view to be rendered. 
A bridge of communication between the models and views 

Models - Facilitates business logic
There are entities of your application that you will address using nouns, like users, tasks, etc. 
Business logic that goes around these models entail the interactions around these things, their relationships
and how they should work inside of the application.
This includes persistence, like saving data into the database.

Views - Presentation layer.
Templates that contain html and mixed with ruby code in order to present to the user the final response. 

```
One of the most common pitfalls is to put business logic in the controllers. We will cover this extensively and Aaron will 'model' good behavior.

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




