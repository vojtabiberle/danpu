Danpu - MySQL dump tool for PHP
=========

[Packagist](https://packagist.org/packages/rah/danpu) | [Downloads](https://github.com/gocom/danpu/releases) | [Issues](https://github.com/gocom/danpu/issues) | [Donate](http://rahforum.biz/donate/danpu)

[![Build Status](https://travis-ci.org/gocom/danpu.png?branch=master)](https://travis-ci.org/gocom/danpu)

Danpu is a dependency-free, cross-platform, portable PHP library for backing up MySQL databases. It has no hard dependencies, and is fit for restricted environments where security is key and access is limited. Danpu requires nothing more than access to your database, PDO and a directory it can write the backup to. The script is optimized and has low memory-footprint, allowing it to handle even larger databases.

Danpu supports backing up table structures, the data itself, views and triggers. Created dump files can optionally be compressed to save space, and generated SQL output is optimized for compatibility.

Requirements
---------

Minimum:

* PHP 5.3.0 or newer
* MySQL 4.1.0 or newer
* [PDO](http://php.net/pdo)

Recommended, but optional:

* PHP 5.4.0 or newer
* MySQL 5.0.11 or newer
* [zlib](http://www.php.net/manual/en/book.zlib.php)

Backing up views and triggers requires MySQL 5.0.11 or newer.

Install
---------

Using [Composer](http://getcomposer.org):

    $ composer require rah/danpu:2.6.*

Usage
---------

To create a new backup or import one, configure a new ```Dump``` instance and pass it to one of the worker classes. To begin, first make sure you have included Composer's autoloader file in your project:

```php
require './vendor/autoload.php';
```

If you are already using other Composer packages, or a modern Composer-managed framework, this should be taken care of already. If not, merely add the autoloader to your base bootstrap includes. See [Composer's documentation](http://getcomposer.org) for more information.

### Take a backup

Backups can be created with the ```Export``` class. The class exports the database to a SQL file, or throws exceptions on errors. The file is compressed if the target filename ends to a .gz extension.

```php
use Rah\Danpu\Dump;
use Rah\Danpu\Export;

$dump = new Dump;
$dump
    ->file('/path/to/target/dump/file.sql')
    ->dsn('mysql:dbname=database;host=localhost')
    ->user('username')
    ->pass('password')
    ->tmp('/tmp');

new Export($dump);
```

The database is dumped in chunks, one row at the time without buffering huge amount of data to the memory. This makes the script very memory efficient, and can allow Danpu to handle databases of any size, given the system limitations of course. You physically won't be able backup rows that take more memory than can be allocated to PHP, nor write backups if there isn't enough space for the files.

### Import a backup

Danpu can also import its own backups using the Import class. While the importer works with backups it has made, it doesn't accept freely formatted SQL. The importer is pretty strict about formatting, and expects exactly the same format as generated by Danpu. It expects that values in statements are escaped properly, including newlines, queries have to end to a semicolon and statements preferably should not wrap to multiple lines.

To import a backup, create a new instance of the Import class. It uncompresses any .gz files before importing.

```php
use Rah\Danpu\Dump;
use Rah\Danpu\Import;

$dump = new Dump;
$dump
    ->file('/path/to/imported/file.sql')
    ->dsn('mysql:dbname=database;host=localhost')
    ->user('username')
    ->pass('password')
    ->tmp('/tmp');

new Import($dump);
```

### Options

In addition to the mandatory connection and file location, Danpu accepts various optional configuration options. These include filtering tables and views by prefix, ignoring tables and creating dumps without row data. See the [src/Rah/Danpu/Config.php](https://github.com/gocom/danpu/blob/master/src/Rah/Danpu/Config.php) for full list of options. The source file contains detailed documentation blocks, outlining each option.

Troubleshooting
---------

### Running out of memory, backup taking too long

As with any PHP script, Danpu is restricted by the safe limits you have set for PHP. When dealing with larger databases, taking a backup will take longer and require more memory. If you find yourself hitting the maximum execution time or running out of memory, its time to increase the limits enough to let the script to work.

PHP lets you to change these limits with its [configuration options](http://php.net/manual/en/ini.core.php), which can be modified [temporarily during script execution](http://php.net/manual/en/function.ini-set.php), or in global configuration files. The configuration options you will be the most interested in, are [memory_limit](http://www.php.net/manual/en/ini.core.php#ini.memory-limit) and [max_execution_time](http://www.php.net/manual/en/info.configuration.php#ini.max-execution-time), and possibly [ignore_user_abort](http://php.net/manual/en/function.ignore-user-abort.php). You can change these values in your script before running a backup with Danpu:

```php
ini_set('memory_limit', '256M');
set_time_limit(0);
ignore_user_abort(true);
```

This of course requires that you have access to these values and you actually have more memory to give. Keep in mind that PHP may be affected by other limitations, such as your web server.
