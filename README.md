# dck-app

The goal of this app is run a app using docker & docker-compose in DEVELOP environment, this app is
not optimize to use in PRODUCTION environment.

NOTE: This app use [docker-services](https://github.com/ginsen/docker-services) to consume services as MySQL and others
network services.

## Install

Copy and rename .env.dist to .env and edit with your custom params.

```bash
$ cp .env.dist .env
```

Copy your SSH keys for access into the container.

```bash
$ cp ~/.ssh/id_rsa.pub ./docker/bash/ssh/
$ cp ~/.ssh/id_rsa ./docker/bash/ssh/
```

Download your application into this root, **with name app**

```bash
git clone https://github.com/app/your-project-app.git app
```

## Up containers

Now you ar ready to up project with **docker-compose**.
Run this commands:

```bash
$ docker-compose up -d
```

Show containers is up.

```bash
$ docker-compose ps
```

Access into container

```bash
$ docker-compose exec web bash
```

When you access into container you access as root, you need change to normal user profile.

```bash
$ su {yourname}
```

Now you are into your project with your profile user, then you can run command to build your symfony app, remember edit 
the params of your .env app.

```bash
$ composer install
...
# yarn install
# ./bin/console doctrine:schema:create
# ... and other stuff
``` 

Or run test, sonar-scanner, ...

```bash
$ ./bin/phpunit
$ sonar-scanner
```
