Pancake on Heroku
=================

First, we'll create an empty top-level directory for our project:

    $ mkdir hellopancake
    $ cd hellopancake

### Download and extract Pancake

    $ wget http://manage.pancakeapp.com/download/<YOUR-LICENSE-KEY> -O pancake.zip
    $ unzip pancake.zip && mv pancake_3/pancake/* .
    $ rm -r pancake_3 pancake.zip
    $ mv example.htaccess .htaccess

### Store your app in Git

Next, we'll create a new git repository and save our changes:

    $ git init
    $ git add .
    $ git commit -m "Initial commit"

### Deploy your application to Heroku

The next step is to push this repository to Heroku. First, we have to get a place to push to from Heroku. We can do this with the `heroku create` command:

    $ heroku create

This automatically added the Heroku remote for our app to our repository. Now we can do a simple git push to deploy our application:

    $ git push heroku master

### Create database

A ClearDB MySQL database can be attached to a Heroku application via the CLI:

    $ heroku addons:add cleardb

Heroku recommends using the `DATABASE_URL` config variable to store the location of your primary database. Your new database will have already been assigned a `CLEARDB_DATABASE_URL` config variable, so this will set its location in the `DATABASE_URL`:

    $ heroku config:set DATABASE_URL=`heroku config:get CLEARDB_DATABASE_URL`

### Installation

I've created a patch for the Pancake installation wizard that enables you to skip Step 2 (database connection settings):

    $ wget https://raw.github.com/onheroku/pancake/master/heroku-pancake.patch
    $ patch -p0 < heroku-pancake.patch
    $ rm heroku-pancake.patch

Now, commit and deploy this change:

    $ git add installer/controllers/wizard.php
    $ git commit -m "Installation wizard patch"
    $ git push heroku master

..and open the installer:

    $ heroku open

Go through the installation wizard as normal.

### Configure database

Upon completion of the installation wizard, Pancake will write to `system/pancake/config/database.php`. Alas, Heroku's filesystem in ephemeral, so this will only be saved to the current dyno's temporary filesystem. Additionally, this type of setup flies in the face of the Heroku convention of keeping all private or environment-specific data in config variables.

Luckily, the `database.php` file is in, well, PHP, so we can use the magic of programming to solve all our problems. In fact, dear reader, I already did! This should get you up and running:

    $ wget https://raw.github.com/onheroku/pancake/master/database.php
    $ mv database.php system/pancake/config/database.php
    $ git add system/pancake/config/database.php
    $ git commit -m "Database configuration"
    $ git push heroku master

### Conclusion

We can now visit the app in our browser:

    $ heroku open

Have fun!
