# Models and Migrations

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

