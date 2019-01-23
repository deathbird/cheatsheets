# PHP Cheatsheet

## PHP LIBS
https://packagist.org/packages/moneyphp/money

```php
// format date time to update mysql field:
$activeRecord->DtmField = date('Y-m-d G:i:s');

/* pretty print variables */
highlight_string("<?php\n\$data =\n" . var_export($data, true) . ";\n?>"); exit;

/* or */
echo '<pre>' . var_export($data, true) . '</pre>';

//======================= ITERATORS ============================================

// conversion: iterator_to_array()
// DirectoryIterator: clone() the item you want to save to another varialble!!!


//======================= DEBUGGING ============================================

var_dump(debug_trace());  -- dispay backtrace

/* var_dump does not display ellipsis 5 levels deep */
ini_set('xdebug.var_display_max_depth', 5);
var_dump($someObject);

/* xdebug break on php template code */
<?php xdebug_break(); ?>
```  
```ini
/* xdebug useful */
-- xdebug replaces var_dump, var_export with HTML versions!!
-- configurations:
xdebug.var_display_max_depth = 10  -- var_dump does not display ellipsis 5 levels deep
xdegug.collect_vars = 1
xdebug.show_local_vars = 1
xdebug.collect_params = 4
xdebug.profiler_enable_trigger = 1  -- enable xdebug profiler, use only when needed. Use it with webgrind!!

html_errors = On (switch ON conf setting for xdebug output of errors!!!)
xdebug_zval_dump(): var_dump replacement
xdebug_debug_zval(): output type value and ref count.
var_dump(xdebug_get_declared_vars());  -- show lo

/* logging errors conf settings */
log_errors - toggles error logging (default 0, turn on with On)
log_erros_max_length - length in bytes
erro_log - log file for logging (default empty - best practise to a separate file from apache error log)
html_errors = On (switch ON xdebug output of errors!!!)
```  
```php  
/* Register a callback function for execution on shutdown to handle errors,
   Use also error_get_last() to get the last error
 */
register_shutdown_function(array($this, 'onApplicationShutdown'));
$err = error_get_last();

===================================================================================

// alternatively use kint http://kint-php.github.io/kint/
```

## PHP CLI  
```bash
php -i  // prints configuration like phpinfo()

php -m  // php compiled modules
```  

## Composer  

Composer folders:  
global packages (Windows):
C:\Users\<user>\AppData\Roaming\Composer\vendor

Composer SOS on nested projects:  
On hierarchical projects (root project with subprojects eg Sportsbook product that depends on Cms that depends on canvas/OrchestratorClient) if you make a change in composer.json of Cms you must commit it and push to repo in order for the >composer update command to work in the root level!!!!!

Composer commands
```bash
----------------------
# list composer commands
composer list

# help about a command
composer help <command>

# to get a list of your locally available packages or filter by package name
composer show [package-name]

# to get a list of your locally available platform packages
composer show --platform 

# After adding the autoload field in composer.json, you have to re-run dump-autoload to re-generate the vendor/autoload.php file.
composer dump-autoload 

# list config options
composer [global] config --list

# Update composer executable to latest version.
composer self-update

# SHOW DEPENDENCIES FOR A PACKAGE : package name ALL LOWERCASE !!!!
~/projects/www/nlo-toto$ composer depends canvas/cms
canvas/PlayersClub  dev-L10-Orchestrator     requires  canvas/cms (*)                    
canvas/Sportsbook   dev-release-2.0/develop  requires  canvas/cms (dev-r-3.1.0/develop) 

# open in browser package home/repo url
composer browse aws/aws-sdk-php 

# create new projects from an existing package. This is the equivalent of doing a git clone/svn checkout followed by a "composer install" of the vendors.
composer.phar create-project doctrine/orm path 2.2.*

# Deletes all content from Composer's cache directories.
composer clear-cache

# If you think you found a bug, or something is behaving strangely, you might want to run the diagnose command to perform automated checks for many common problems.
composer diagnose
```  

## TDD

Types of testing
* Unit
* Integration
* Functional: Function of your app against a design doc.
* Behaviour: Tests a series of steps to ensure some behaviour in the app is correct.
* Acceptance

Execute phpunit tests:
```bash
# execute all unit tests in 'tests' dir using textdox feature
$ vendor/bin/phpunit tests --verbose --testdox 

# execute all unit tests in 'tests/ReceiptTest.php'
$ vendor/bin/phpunit tests/testsReceipt.php

# execute all unit tests in 'tests' that match a regexp
$ vendor/bin/phpunit tests --filter=testTax
$ vendor/bin/phpunit tests --filter=ReceiptTest::testTax

# using a phpunit.xml config file
$ vendor/bin/phpunit tests --testsuite=app
$ vendor/bin/phpunit tests --testsuite=app --filter=testTax

# using one dataset from a data provider
$ vendor/bin/phpunit tests --filter=testTotal#1

# two datasets from data provider
$ vendor/bin/phpunit tests --filter=testTotal#1-2

# one dataset from data provider with dataset key!
$ vendor/bin/phpunit tests --filter='testTotal@ints totaling 16'
```  

### Testing Concepts  
A. Test Double:   
Replace a production object for testing purposes (for isolation testing to 
  replace a dependency, ensure a condition occurs, improve test performance). Doubles should
  enable our tests to be fast, isolated and with full code coverage!!! Test Doubles can be:  
  1. Dummy: An input object typically not needed or used in the test.  
  2. Fake: A simplified version of an object to achieve speed impovements or eliminate side effects.  
  3. Stub: Provides a preset answer to method calls that we decided ahead of time.  
  4. Spy: Acts as a higher level Stub recording also information about what happened with this T.Double.  
  5. Mock: A higher level Stub preprogrammed with expectations including the ability to both respond to
           calls the know about  
  6. Fixture: A higher level Stub to test Database features using fake db records.  

B. Arrange, Act, Assert Pattern:    
   Arrange the creation of any dependencies  
   Act on the code you are testing  
   Assert the outcomes you are expecting  

C. TDD workflow:   
write failing test first and then the code to pass the test.  

D. Testing Legacy Applications    
  How we decide what to test in an existing legacy app???  
  1. First write tests for critical functionality (doesn't break on new code pushes).  
  2. When bugs are fixed write also tests to guarantee they are not coming back.  
  3. Anything new functionality added must be paired with a test.  
 
