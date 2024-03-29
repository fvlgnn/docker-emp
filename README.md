# docker-emp
Docker environement stack with NGINX, MYSQL (MariaDB), PHP (FPM)


## Overview

Requirement **docker-compose** 


### Stack Setup

* Nginx version: 1.14
* PHP version: 7.3
* MariaDB version: 10.3


### Docker Images

* nginx:1.14.2-alpine
* php:7.3-fpm-alpine
* mariadb:10.3


### Tree

```
docker-emp
│   .env
│   .gitignore
│   docker-compose.yml
│   LICENSE
│   Makefile
│   README.md
│   
├───src
│       index.php
│       info.php
│       
├───db        
├───docker
│   ├───mariadb
│   │   │   Dockerfile
│   │   │   
│   │   └───init
|   |           000-operations.sql
│   │           dbapp.sql
|   |           zzz-operations.sql
│   │           
│   ├───nginx
│   │   │   default.template
│   │   │   Dockerfile
│   │   │   
│   │   └───certs
│   │           
│   └───php
│           Dockerfile
│           php.ini
│           www.conf
│           
└───logs
    ├───db
    ├───php
    └───web
```


### Environment Variables

#### Environment file: `.env`

_Edit or view this file before building the stack_

**Variable description**

* `PROJECT_NAME`: Name of your docker stack
* `VERSION_ID`: ID of your project stack. (Use it only if you want try more version of your project)
* `XDEBUG`: Using `1` if you want install xdegub module, otherwise using `0`.
* `COMPOSER`: Using `1` if you want install php composer, otherwise using `0`.
* `TIME_ZONE`: Time zone, using form PHP and MySQL. 
* `WEBAPP_URL`: Url of your web app. Using for CMS/Frameweork like Wordpress, CodeIgniter, etc.
* `HTTP_PORT_EXPOSED`: Exposed port of you're HTTP web app.
* `HTTPS_PORT_EXPOSED`: Exposed port of you're HTTPS SSL web app. Required certificates.
* `SERVER_HOST_NAME`: The server name. For security reasons it's preferable to use the DNS name (without http/https) or the host name. (ex. localhost | domain.dom | www.domain.dom | sub.domain.dom | my-laptop).  
* `DB_PORT_EXPOSED`: Used only if you want connetc to database with external client. It's port number used for connetcting with external client like _HeidiSQL_ or _MySQL Workbench_ to create database and tables. Configure external client connetction using _host_ `127.0.0.1`, _username_ `root` whereas _password_ your `DB_ROOTPASS` and _port_ your `DB_PORT_EXPOSED` as in environment file.
* `DB_ROOTPASS`: MySQL root password. It's not safe use this method in production but for development it's acceptable.
* `DB_USER`: User name for connetion database from your webapp.
* `DB_USER`: User password for connetion database from your webapp.
* `DB_NAME`: Database of your webapp. Will be created at the build. Otherwise you can create database from docker exec or external client.


### Workspace

#### Webapp foleder: `src`

This is you WebApp folder. Put here your PHP project (CMS/Framework/Sources).

For connetcting to databse with Framework or source code use `getenv()` variables (view `index.php` source).

```php
$dbhost = getenv('DB_HOST');
$dbuser = getenv('DB_USER');
$dbpass = getenv('DB_PASS');
```

For install a CMS with wizard setup use:

* _hostname_: `db` 
* _database_: _the environment variable `DB_NAME` as in environment file_ 
* _username_: _the environment variable `DB_USER` as in environment file_ 
* _password_: _the environment variable `DB_PASS` as in environment file_ 
* _port_: `3306` _it's important to declare it!_

#### Database folder: `db`

If you haven't this folder, will be it create at the first build.

`db` folder contains your project's database, don't modify the files inside it. If you need to move your project enviroment, remember to move this folder with its content.
If you don't want the databases to be inside the project folder use the docker-compose volumes.

#### Logs folder: `logs`

Writing logs to files is disabled by default. To enable it, read the Note.


### Note

---

The stack has also been tested with PHP 7.1 and MySQL 5.7, MariaDB 10.*. You can change these variables directly in the Dockerfile.


---

The first time of build after finish if you not connect to database aptendm some minute because the container must be inizilize all system.


---

If you want to use persistent and encrypted databases storage, you can use the `docker volumes` by editing the `docker-compose.yml` as below.

```yaml

# ...
services:
# ...
  db:
# ...
    volumes:
      # - ./db:/var/lib/mysql
      - database:/var/lib/mysql
# ...

# add after services
volumes:
  database:

```

---

For enable DB logs edit `docker/mariadb/Dockerfile` uncomment this command

```
RUN sed -i "s/#log-error/log-error/g" /etc/mysql/mysql.conf.d/mysqld.cnf
```

---

If you've a database dump you can add the `.sql`, `.gz` dump file in `docker\mariadb\init` and uncomment the follow row. The database will be created at the first build.

```yaml

# ...
services:
# ...
  db:
# ...
    volumes:
      - ./docker/mariadb/init:/docker-entrypoint-initdb.d
# ...

```

Also, if you need to perform other operations on databases at the first build, you can edit the following files by entering the queries that you need:

  * `docker\mariadb\init\000-operations.sql`
  * `docker\mariadb\init\zzz-operations.sql` 

These operations will only be performed on the first build

---

When stack is ready visit `http://localhost`

For show PHP info go to `http://localhost/info.php`



## Build and Run

If you're on linux system you can run:

* `make install` - builds and init your stack
* `make start` - start your stack
* `make stop` - stop your stack 
* `make remove` - remove your stack but mantain your databases and logs
* `make purge` -  remove your stack and delete all databases and clear logs files
* `make validate` - validate your docker-compose.yml file if you're do any changes
* `make bash-db` - enter with bash into db container 
* `make bash-php` - enter with bash into php container 
* `make bash-web` - enter with bash into web container 

Command in nutshell for build, start, stop, remove and validate the docker stack.

* `docker-compose up -d --build` - builds and init your stack
* `docker-compose down` - remove your stack but mantain your databases and logs
* `docker-compose start` - start your stack
* `docker-compose stop`  - stop your stack 
* `docker-compose down --volumes` - like _down_ but also removes volumes
* `docker-compose config` - validate your stack

* `docker-compose exec SERVICE_NAME bash` enters with bash terminal into container

