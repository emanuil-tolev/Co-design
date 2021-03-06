Co-design
=========

This is pilot software for the Co-design programme, based on 
[Project REALISE](https://github.com/AccessAtECS/Project-REALISE).

Database Setup
--------------
For any commands below: note that `mysql -u root` will try to login as root
_without_ a password (and fail if root does have a password).
Use `mysql -u root -p` in these circumstances.

Create a MySQL database called `codesign_production` and a user login to
access it (you may need to be mysql-root to do this):

    $ mysql -u root 
    mysql> CREATE DATABASE codesign_production;
    mysql> GRANT ALL PRIVILEGES ON codesign_production.* TO SOME-USER@localhost IDENTIFIED BY 'SOME-PASSWORD';
    mysql> quit

Next, build the database tables by running the `sql/codesign_production.sql`
script:

    $ mysql -u root codesign_production < sql/codesign_production.sql

Create some test data in the database (optional). Note that if you run the
application without any data, you may get error messages on the homepage:

    $ mysql -u root codesign_production < sql/codesign_production_testdata.sql

Application Configuration
-------------------------

Create a `system_configuration.php` in the root application folder (the same 
folder as this file is in). It should define the database connection
parameters, e.g.

    <?php
    define('MYSQL_SERVER', '0.0.0.0');
    define('MYSQL_USERNAME', 'SOME-USER');
    define('MYSQL_PASSWORD', 'SOME-PASSWORD');
    define('MYSQL_SCHEMA', 'codesign_production');
    ?>

Edit `app/system/core/conf.php` and set these variables to the full local path
of this repository. (Keep the `/app/` suffix in the first variable):

    define("SYSTEM_DIR", "/Users/martyn/development/co-design/app/");
    ...
    define('SYS_ROOTDIR', "/Users/martyn/development/co-design/");


Create a `.htaccess` file in the root application folder. The application
should be served with Apache. Ensure the file permissions on the `.htaccess`
file are appropriate. Configure the following rewrite rules, to ensure
requests are processed by index.php:

    # .htaccess file for co-design
    RewriteEngine on

    # don't rewrite existing files (e.g. images, javascript)
    RewriteCond %{REQUEST_FILENAME} !-f

    # don't rewrite existing directories
    RewriteCond %{REQUEST_FILENAME} !-d

    # rewrite all other requests to index.php
    RewriteRule ^(.*)$ /index.php?p=$1
