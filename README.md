Period
============

[![Author](http://img.shields.io/badge/author-@nyamsprod-blue.svg?style=flat-square)](https://twitter.com/nyamsprod)
[![Latest Version](https://img.shields.io/github/release/nyamsprod/Period.svg?style=flat-square)](https://github.com/nyamsprod/Period/releases)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)<br>
[![Build Status](https://img.shields.io/travis/nyamsprod/Period/master.svg?style=flat-square)](https://travis-ci.org/nyamsprod/Period)
[![Coverage Status](https://img.shields.io/scrutinizer/coverage/g/nyamsprod/Period.svg?style=flat-square)](https://scrutinizer-ci.com/g/nyamsprod/Period/code-structure)
[![Quality Score](https://img.shields.io/scrutinizer/g/nyamsprod/Period.svg?style=flat-square)](https://scrutinizer-ci.com/g/nyamsprod/Period)
[![Total Downloads](https://img.shields.io/packagist/dt/bakame/period.svg?style=flat-square)](https://packagist.org/packages/bakame/period)

This class is based on [Resolving Feature Envy in the Domain](http://verraes.net/2014/08/resolving-feature-envy-in-the-domain/) by Mathias Verraes and helps resolve many recurrent issues around Date range selection and usage.

This package is compliant with [PSR-2], and [PSR-4].

[PSR-2]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md
[PSR-4]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md

System Requirements
-------

You need **PHP >= 5.3.0** to use `Period` but the latest stable version of PHP is recommended.

Install
-------

Install `Period` using Composer.

```json
{
    "require": {
        "bakame/period": "1.*"
    }
}
```
#### Going Solo

You can also use `Period` without using Composer by downloading the library and registing an autoloader function:

```php
spl_autoload_register(function ($class) {
    $prefix = 'Period\\';
    $base_dir = __DIR__ . '/src/';
    $len = strlen($prefix);
    if (strncmp($prefix, $class, $len) !== 0) {
        // no, move to the next registered autoloader
        return;
    }
    $relative_class = substr($class, $len);
    $file = $base_dir . str_replace('\\', '/', $relative_class) . '.php';
    if (file_exists($file)) {
        require $file;
    }
});
```

Or, use any other [PSR-4](http://www.php-fig.org/psr/psr-4/) compatible autoloader.

Documentation
------

`Period` is [fully documented](http://nyamsprod.github.io/Period/). You can contribute to its documentation in the [gh-pages branch](https://github.com/nyamsprod/Period/tree/gh-pages).

Usage
-------

#### Period::__construct($start, $end)

Both `$start` and `$end` parameters are `DateTime` objects or strings parsable by the `DateTime` constructor. They represent the range endpoints 

- The `$start` endpoint represent **the starting included endpoint**.
- The `$end` value represents **the ending excluded endpoint**. `$end` MUST BE greater or equals to `$start` or the instantiation will failed. 


```php
use Period\Period;

$period = new Period('2012-04-01 08:30:25', new DateTime('2013-09-04 12:35:21'));
```

To ease instantiation the class comes with many named constructors to ease its instantiation:

#### Period::createFromDuration($datetime, $interval)

returns a `Period` object which starts at `$datetime` with a duration equals to `$interval`

- The `$datetime` parameter is a `DateTime` object or a string parsable by the `DateTime` constructor;
- The `$interval` parameter can be:
    - a `DateInterval` object
    - a string parsable by the `DateInterval::createFromDateString` method.
    - an integer interpreted as the number of seconds after which the period MUST be considered over

```php
use Period\Period;

$period  = Period::createFromDuration('2012-04-01 08:30:25', '1 DAY');
$period2 = Period::createFromDuration('2012-04-01 08:30:25', new DateInterval('P1D'));
$period3 = Period::createFromDuration('2012-04-01 08:30:25', 86400);
```
#### Period::createFromWeek($year, $week)

returns a `Period` object with a duration of 1 week for a given year and week.

- The `$year` parameter is a valid year;
- The `$week` parameter is a selected week (between 1 to 53);

```php
use Period\Period;

$period  = Period::createFromWeek(2013, 23);
//this period represents the 23rd week of 2013
```

#### Period::createFromMonth($year, $month)

returns a `Period` object with a duration of 1 month for a given year and month. 

- The `$year` parameter is a valid year;
- The `$month` parameter is a selected month (between 1 to 12);

```php
use Period\Period;

$period  = Period::createFromMonth(2013, 7);
//this period represents the month of July 2013
```

#### Period::createFromQuarter($year, $quarter)

returns a `Period` object with a duration of 3 months for a given year and quarter. 

- The `$year` parameter is a valid year;
- The `$quarter` parameter is a selected quarter (between 1 to 4);

```php
use Period\Period;

$period  = Period::createFromQuarter(2013, 2);
//this period represents the second quarter of 2013
```

#### Period::createFromSemester($year, $semester)

returns a `Period` object with a duration of 6 months for a given year and semester. 

- The `$year` parameter is a valid year;
- The `$semester` parameter is a selected semester (between 1 and 2);

```php
use Period\Period;

$period  = Period::createFromSemester(2011, 1);
//this period represents the first semester of 2013
```

#### Period::createFromYear($year)

returns a `Period` object with a duration of 1 year for a given year.

- The `$year` parameter is a valid year;

```php
use Period\Period;

$period  = Period::createFromYear(1971);
//this period represents the year 1971
```

Once you have a instantiated `Period` object you can access its property using getter methods:

#### Period::getStart()

Returns the starting **included** endpoint as a `DateTime`.

#### Period::getEnd();

Returns the ending **excluded** endpoint as a `DateTime`.

#### Period::getDuration()

Returns the period duration as a `DateInterval` object.

#### Period::getRange($interval)

Returns a `DatePeriod` object that lists `DateTime` objects inside the period separeted by the given `$interval`. 

The `$interval` parameter is a `DateInterval` or a string parsable by the `DateInterval::createFromDateString` method.

```php
use Period\Period;

$period  = Period::createFromYear(1971);
foreach ($period->getRange('1 MONTH') as $datetime) {
    echo $datetime->format('Y-m-d H:i:s');
}
//will iterate 12 times
```

#### Period::contains($datetime)

Tells whether a `$datetime` is contained within the `Period` or not.

The `$datetime` parameter is a `DateTime` object or a string parsable by the `DateTime` constructor

```php
use Period\Period;

$period = Period::createFromMonth(1983, 4);
$period->getStart(); //returns DateTime('1983-04-01');
$period->getEnd(); //returns DateTime('1983-05-01');
$period->contains('1983-04-15'); //returns true;
$period->contains($period->getEnd()); //returns false because of `getEnd` definition;
```

#### Period::overlaps(Period $period)

Tells whether two `Period` objects overlap each other or not.

```php
use Period\Period;

$period1 = Period::createFromMonth(2014, 3);
$period2 = Period::createFromMonth(2014, 4);
$period3 = Period::createFromDuration('2014-03-15', '3 WEEKS');

$period1->overlaps($period2); //return false
$period1->overlaps($period3); //return true
$period2->overlaps($period3); //return true
```

The `Period` object is an Immutable Value Object so any change to its property returns a new `Period` class. 

#### Period::startingOn($datetime)

Returns a new `Period` object with an updated starting included endpoint.

The `$datetime` parameter is a `DateTime` object or a string parsable by the `DateTime` constructor

```php
use Period\Period;

$period    = Period::createFromMonth(2014, 3);
$newPeriod = $period->startingOn('2014-02-01');
$period->getStart(); //returns DateTime('2014-03-01');
$newPeriod->getStart(); //returns DateTime('2014-02-01');
// $period->getEnd() equals $newPeriod->getEnd();
```

#### Period::endingOn($datetime)

Returns a new `Period` object with an updated excluded endpoint.

The `$datetime` parameter is a `DateTime` object or a string parsable by the `DateTime` constructor

```php
use Period\Period;

$period    = Period::createFromMonth(2014, 3);
$newPeriod = $period->EndingOn('2014-03-16');
$period->getEnd(); //returns DateTime('2014-04-01');
$newPeriod->getEnd(); //returns DateTime('2014-03-16');
// $period->getStart() equals $newPeriod->getStart();
```

#### Period::withDuration($interval)

Returns a new `Period` object by updating its duration. The excluded endpoint is updated, the starting included endpoint remains the same.

The `$interval` parameter can be:

- a `DateInterval` object
- a string parsable by the `DateInterval::createFromDateString` method.
- an integer interpreted as the number of seconds after which the period MUST be considered finished


```php
use Period\Period;

$period    = Period::createFromMonth(2014, 3);
$newPeriod = $period->withDuration('2 WEEKS');
$period->getEnd(); //returns DateTime('2014-04-01');
$newPeriod->getEnd(); //returns DateTime('2014-03-16');
// $period->getStart() equals $newPeriod->getStart();
```

#### Period::merge(Period $period)

Merge two `Period` objects by returning a new `Period` object which starting endpoint is the smallest and the excluded endpoint is the biggest between both objects.

```php

use Period\Period;

$period    = Period::createFromSemester(2012, 1);
$altPeriod = Period::createFromWeek(2013, 4);
$newPeriod = $period->merge($altPeriod); 
// $newPeriod->getStart() equals $period->getStart();
// $newPeriod->getEnd() equals $altPeriod->getEnd();
```

Testing
-------

``` bash
$ phpunit
```

Contributing
-------

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

Credits
-------

- [ignace nyamagana butera](https://github.com/nyamsprod)
- [All Contributors](https://github.com/nyamsprod/Bakame.Tools/graphs/contributors)