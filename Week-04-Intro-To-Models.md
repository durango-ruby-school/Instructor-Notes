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

Take a look at directory structure created by rails new command.
One of the most important directories n your rails app is the app/ directory. You will spend the most time in this directory.
Inside this we have the following direcotries:

+ controllers - contains the controller files
+ views - contains templates, layouts, partials and view files 
+ models - contains Active Record and model class files.
+ assets - images, javascripts, stylesheets we write for our applications.
+ helpers - classes that contain addtional methods that you can add to your views
+ mailers - contain templates and methods used for sending emails from your app.  



