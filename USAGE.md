This is just one way to use it.

    # Generate your directory structure.
    $ mkdir -p /var/www/my_project/{src,data/mysql,logs/mysql,logs/nginx}
    $ cd /var/www/my_project
    
    # This should be your directory structure.
    $ tree
    ├── data
    │   └── mysql
    ├── logs
    │   ├── mysql
    │   └── nginx
    └── src
    
    # Clone Laradock.
    $ git clone https://github.com/housni/laradock.git
    
    # Clone Laravel into the 'src' dir.
    $ git clone git@github.com:laravel/laravel.git src
    
    $ cd /var/www/my_project/laradock
    
    # In some instances, you might have to restart docker so that it builds the correct network bridges.
    $ sudo service docker restart
    
    # Run Nginx and MySQL in Docker. This won't work if you have Apache/Nginx or MySQL running locally because by default, we are using the default ports (80 & 3306). So, make sure you stop those services first.
    $ docker-compose up -d nginx mysql

    # From the host machine, find your Dockers IP address so that you can use it in .env to connect to your database which is also on Docker.
    # For Windows & Mac:
    $ docker-machine ip default
    # For Linux:
    $ ifconfig docker0 | grep 'inet' | cut -d: -f2 | awk '{ print $1}' | head -n1
    # By default, the IP is 172.17.0.1
    
    # Enter Workspace in case you need to run composer update or any of the artisan commands, etc.
    $ docker-compose run workspace bash
    
    # Once inside the 'workspace' docker instance, you can set up Laravel:
    $ chmod -R 777 storage bootstrap/cache
    $ composer install
    
    # Now, create the .env file...
    $ cp .env.example .env
    # ...and edit it to add your database credentials.
    $ vim .env

    # Generate a key.
    $ php artisan key:generate

    # Create your database.
    $ php artisan migrate

    # Since set up is complete, we can exit the workspace.
    $ exit

    # You can view the Docker instances running on your computer.
    $ docker-compose ps

    # Here's what I see:
    $ docker-compose ps
             Name                      Command             State                     Ports                   
    --------------------------------------------------------------------------------------------------------
    laradock_application_1   true                          Exit 0                                            
    laradock_data_1          true                          Exit 0                                            
    laradock_mysql_1         docker-entrypoint.sh mysqld   Up       0.0.0.0:3306->3306/tcp                   
    laradock_nginx_1         nginx                         Up       0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp 
    laradock_php-fpm_1       php-fpm                       Up       9000/tcp                                 
    laradock_workspace_1     /sbin/my_init                 Up                                                

To access your app, open your browser and go to http://127.0.0.1/

## Create a new MySQL database
If you want to create a new MySQL database, you'll have to connect to the Docker MySQL instance using the default username (homstead), password (secret) and the Docker IP you figured out earlier (you entered this in .env). My Docker IP is 172.17.0.1, so:

     mysql -u homestead -psecret -h 172.17.0.1

You can even configure your MySQL client (phpMyAdmin, MySQL Workbench, etc) to connect to it.
Once you're in, you can create the databases you need.

**NOTE: The 'docker-compose' commands can only be run from inside the 'laradock' directory since that's the directory that contains the main docker-compose.yml file.**

## Directories
 - src - this is where your Laravel source code will live. For example, .env.example should exist in src/.env.example
 - data/mysql - this is where your MySQL data files will be saved.
 - logs/mysql - this is where your MySQL logs will eventually be saved (TODO).
 - logs/nginx - this is where the Nginx logs will be saved.


## More Information
See parent repo for more information: https://github.com/LaraDock/laradock/blob/master/README.md
