---
title: PostgreSQL
layout: page
tags:
  - services
  - databases
  - postgresql
categories:
  - databases
---
* include a table of contents
{:toc}

The default databases created for you are **development** and **test**.

PostgreSQL ```9.2.4``` runs on the default port and the credentials are stored in the ```PG_USER``` and ```PG_PASSWORD``` environment variables.

We install the Ubuntu postgresql-contrib package. It includes the [extension modules](http://www.postgresql.org/docs/9.2/static/contrib.html) listed in the PostgreSQL Documentation.

You need to activate them with ```CREATE EXTENSION``` as explained in the [Extension Guide](http://www.postgresql.org/docs/9.1/static/sql-createextension.html).

## Versions

### 9.2

Version **9.2** of PostgreSQL runs on the default port of ```5432```.

### 9.3

In addition we also have version **9.3** installed and configured identically. You can use this version by specifying port ```5433``` in your database configuration.

## Create Databases and run psql commands
You can run any SQL query against the PostgreSQL database. For example to create a new database:

~~~shell
psql -c 'create database new_db;'
~~~

## Enable Extensions
You can enable extensions either in your database migrations or by running the commands against the database. For example, to enable hstore for the test database:

~~~shell
psql -c 'create extension hstore;' -d test
~~~

in your setup commands.

## PostGIS
PostGIS 2.0.x is installed on the virtual machine.

## Ruby on Rails

We replace the values in your `config/database.yml` automatically.

If you have your Rails application in a subdirectory or want to change
it from our default values you can add the following to a codeship.database.yml
(or any other filename) in your repository:

~~~yaml
development:
  adapter: postgresql
  host: localhost
  encoding: unicode
  pool: 10
  username: <%= ENV['PG_USER'] %>
  template: template1
  password: <%= ENV['PG_PASSWORD'] %>
  database: development<%= ENV['TEST_ENV_NUMBER'] %>
  port: 5432
  sslmode: disable
test:
  adapter: postgresql
  host: localhost
  encoding: unicode
  pool: 10
  username: <%= ENV['PG_USER'] %>
  template: template1
  password: <%= ENV['PG_PASSWORD'] %>
  database: test<%= ENV['TEST_ENV_NUMBER'] %>
  port: 5432
  sslmode: disable
~~~

Then in your setup commands run

~~~shell
cp codeship.database.yml YOUR_DATABASE_YAML_PATH
~~~

to copy the file wherever you need it.

If you don't use Rails and load the database.yml yourself you might see an error like the following:

~~~shell
PSQL::Error: Access denied for user '<%= ENV['PG_USER'] %>'@'localhost'
~~~

The database.yml example has ERB syntax in it so you need to load it by interpreting the ERB first:

~~~ruby
DATABASE_CONFIG = YAML.load(ERB.new(File.read("config/database.yml"))).
~~~

## Django

~~~python
DATABASES = {
  'default': {
    'ENGINE': 'django.db.backends.postgresql_psycopg2',
    'NAME': 'test',
    'USER': os.environ.get('PG_USER'),
    'PASSWORD': os.environ.get('PG_PASSWORD'),
    'HOST': '127.0.0.1',
  }
}
~~~
