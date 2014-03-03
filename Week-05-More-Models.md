# Week 5 - Deploying to heroku

## Setting up Postgres
http://help.nitrous.io/postgresql/

Also available from the AutoParts menu

```
$ parts install postgresql

$ parts start postgresql
```

### Create rails app and set postgres as default database
from command-line

```
$ rails _3.2.17_ new my_site -d postgresql
```
