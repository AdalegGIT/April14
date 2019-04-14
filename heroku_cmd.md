First things first, create an account at [Heroku](https://www.heroku.com/). Then you need to install [Heroku Toolbelt](https://toolbelt.herokuapp.com/) so you can use it on the command line. Once you've done that, you want to login: 

```bash
$ heroku login
```

Enter in your credentials. If you do not have a SSH public key, then you'll need to create one by typing:

(It's really likely that you already have one from using GitHub)

```bash
$ ssh-keygen -t rsa
```

And then you need to add that public key to your Heroku account:

```bash
$ heroku keys:add
```

You can read more about this [here](https://devcenter.heroku.com/articles/keys). 

After that you want to make your path to the rails app, and then type:

```bash
$ heroku create
```

This will setup your repository for deployment to Heroku. But whoa there Speedy, not so fast! Our application uses SQLite for the database. Heroku does not like SQLite, we have to change it to use PostgreSQL.

This means another commit. 

First move `gem 'sqlite3'`in the Gemfile (that's actually what the file is called) into the development section: 

```ruby
group :development do
  gem 'sqlite3'
end

group :production do
  gem 'pg', '0.21.0'
end
```

Then you want to configure the database to use PostgreSQL in production. This means opening up `config/database.yml` and making `production:` this: 

```ruby
adapter: postgresql
database: picLab
host: localhost
```

So that the whole file looks like this:

```ruby
# SQLite version 3.x
#   gem install sqlite3
#
#   Ensure the SQLite 3 gem is defined in your Gemfile
#   gem 'sqlite3'
development:
  adapter: sqlite3
  database: db/development.sqlite3
  pool: 5
  timeout: 5000

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  adapter: sqlite3
  database: db/test.sqlite3
  pool: 5
  timeout: 5000

production:
  adapter: postgresql
  database: picLab
  host: localhost
```

Now you want to run:

```bash
$ bundle install
```

...and then commit.

Now you are ready to deploy your application to Heroku. Just type:

```bash
$ git push heroku master
```

After Heroku is done its thing, they'll give you a URL to which your app has been deployed. You need to do one last thing, you need to setup your database on the server:

```bash
$ heroku run rails db:migrate
$ heroku run rails db:seed
```

Now visit the URL and there for you and the rest of the world to behold is your application! 