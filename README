PHP 8.3-fpm
Composer 2
MYSQL 8
NODE 20.18

# Installation
`docker compose build`
or
`docker compose up -d`


# Configure

## nginx
DocumentRoot supposed to be app/public on host machine and /var/www/html inside docker container.
Change it in docker-compose.yml if you need.

## xdebug
Port 9003
Sometimes default host.docker.internal:host-gateway not works properly. Try to change host to 127.0.0.1:9003.
Default idekey VSCODE. To change it go to ./docker/php/20-xdebug.ini.

### VSCODE
launch.json example
```JSON
{
    "version": "0.2.0",
    "configurations": [
    //
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "hostname": "127.0.0.1",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html": "${workspaceFolder}"
            },
            "ignore": [
                "**/vendor/**/*.php"
            ]
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "hostname": "127.0.0.1",
            "port": 9003,
            "program": "${file}",
            "cwd": "${fileDirname}",
            "ignore": [
                "**/vendor/**/*.php"
            ]
        }
    ]
}
```
Don't forget about pathMappings


# How to work
First. You need to start `docker compose up -d` and connect to container `docker compose exec php /bin/bash`
Then you can install any framework from https://packagist.org/
All content will appear in app folder.

## Tips
- create alias inside container for `php bin/console` like: `alias pbc='php bin/console'`. Then you can work like this `pbc make:controller` against `php bin/console make:controller`


# Git
I suppose you will init git in this folder on your host.
`git init`
By default vendor and node_module folders excluded.