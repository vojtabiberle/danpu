Danpu - MySQL dump tool for PHP
=========

[Packagist](https://packagist.org/packages/rah/danpu) | [Twitter](http://twitter.com/gocom) | [Donate](http://rahforum.biz/donate/danpu)

Danpu is a dependency-free, cross-platform, portable PHP library for backing up MySQL databases. It has no hard dependencies, and is fit for restricted, shared-hosting environments. It requires nothing more than access to your database, PDO and a directory it can write the backup to. The script is optimized and has low memory-footprint, allowing it to handle even larger databases.

Installing
---------

Using [Composer](http://getcomposer.org):

    $ composer.phar require rah/danpu

Usage
---------

To create a new backup and or import one, create a ```Dump``` instance containing your configuration options and feed it to one of the worker classes. All classes throw exceptions on failures.

### Taking a backup

Backups are created by the Export class. The class exports the database to a SQL file. The backup is compressed if the target filename ends to .gz extension.

```php
use Rah\Danpu\Dump;
use Rah\Danpu\Export;

$dump = new Dump;
$dump
    ->file('/path/to/target/dump/file.sql')
    ->db('database')
    ->host('localhost')
    ->user('username')
    ->pass('password')
    ->temp('/tmp');

new Export($dump);
```

### Importing a backup

Danpu can also import its own backups it has created. While the importer works with the backups it has made, it doesn't accept freely formatted SQL. The importer is pretty strict about formatting, and expects exactly the same format as generated by Danpu. It requires that values in statements are escaped properly, including newlines, queries have to end to a semicolon and statements preferably should not wrap to multiple lines.

Backups are imported by the Import class. It uncompresses any .gz files before importing.

```php
new Rah\Danpu\Import($dump);
```

Where the ```$dump``` would be an instance of Rah\Danpu\Dump.

Changelog
---------

### Version 2.1.0 - 2013/05/13

* Added: Option to ignore database tables.

### Version 2.0.1 - 2013/05/11

* Fixed: Error in the composer.json.

### Version 2.0.0 - 2013/05/11

* Initial release.