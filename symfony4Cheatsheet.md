# Symfony 4 cheatsheet

## Links
* [recipes repo](https://symfony.sh/)  
* [twig](https://twig.symfony.com/)  

Run local server
```bash
# either:

$ php -S 127.0.0.1:8000 -t public

# or first install alternative:

php ../composer.phar require server --dev

# and then run it:

$ ./bin/console server:run
```  


## Symfony console
```bash  
# show app routes
$ ./bin/console debug:router

# classes & interfaces can be used as type-hints for SERVICES when autowiring
$./bin/console debug:autowiring

# start symfony web server
$ ./bin/console server:run

# example dump of all available configuration options by running for package/bundle (eg <package/bundle>=framework
# dumps options for config/packages/framework.yaml
$ php bin/console config:dump-reference <package/bundle>
$ php bin/console config:dump-reference framework

# full list of available service in service container (--show-hidden to display services with ID starting with a dot)
php bin/console debug:container --show-hidden

# You can get more detailed information about a particular service by specifying its id:
$ php bin/console debug:container 'App\Service\Mailer' --show-arguments

# remove symfony cache
$ rm -rf /var/cache/dev/*
```  

## Concepts  

Recipe: which is a set of automated instructions that tell Symfony how to integrate an external package. Flex recipes exist for many packages and have the ability to do a lot, like adding configuration files, creating directories, updating .gitignore and adding new config to your .env file. Flex automates the installation of packages so you can get back to coding.

