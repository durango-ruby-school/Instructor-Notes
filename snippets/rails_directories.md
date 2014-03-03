# Rails directories

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
