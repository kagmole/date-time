Brick\DateTime
==============

<img src="https://raw.githubusercontent.com/brick/brick/master/logo.png" alt="" align="left" height="64">

A powerful set of immutable classes to work with dates and times.

[![Build Status](https://secure.travis-ci.org/brick/date-time.svg?branch=master)](http://travis-ci.org/brick/date-time)
[![Coverage Status](https://coveralls.io/repos/brick/date-time/badge.svg?branch=master)](https://coveralls.io/r/brick/date-time)
[![Latest Stable Version](https://poser.pugx.org/brick/date-time/v/stable)](https://packagist.org/packages/brick/date-time)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](http://opensource.org/licenses/MIT)

Introduction
------------

This library builds an extensive API on top of the native PHP date-time classes, and adds missing concepts such as `LocalDate`, `LocalTime`, `YearMonth`, `MonthDay`, etc. It adds a nanosecond precision to times, usually limited to a 1 second precision.

The classes follow the [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) standard for representing date and time concepts.

This component follows an important part of the JSR 310 (Date and Time API) specification from Java.
Don't expect an exact match of class and method names though, as a number of differences exist for technical or practical reasons.

All the classes are immutable, they can be safely passed around without being affected.

Installation
------------

This library is installable via [Composer](https://getcomposer.org/).
Just define the following requirement in your `composer.json` file:

```json
{
    "require": {
        "brick/date-time": "0.1.*"
    }
}
```

Requirements
------------

This library requires PHP 7.1 or later.

### HHVM support

HHVM support is in the works, we are waiting for the HHVM team to fix these bugs:

- #3637 [DateTimeZone constructor does not accept time-zone offsets](https://github.com/facebook/hhvm/issues/3637)
- ~~#3650 [DateTime complains about default timezone even when a timezone is given explictly](https://github.com/facebook/hhvm/issues/3650)~~
- ~~#3651 [Incompatible handling of date-times during DST transitions](https://github.com/facebook/hhvm/issues/3651)~~
- #6954 [Typed variadics not supported in PHP code](https://github.com/facebook/hhvm/issues/6954)


Project status & release process
--------------------------------

While this library is still under development, it is well tested and should be stable enough to use in production environments.

The current releases are numbered `0.x.y`. When a non-breaking change is introduced (adding new methods, optimizing existing code, etc.), `y` is incremented.

**When a breaking change is introduced, a new `0.x` version cycle is always started.**

It is therefore safe to lock your project to a given release cycle, such as `0.1.*`.

If you need to upgrade to a newer release cycle, check the [release history](https://github.com/brick/date-time/releases) for a list of changes introduced by each further `0.x.0` version.


Overview
--------

### Main classes

The following classes represent the date-time concepts:

- `DayOfWeek`: a day-of-week such as Monday
- `Duration`: a duration measured in seconds and nanoseconds
- `Instant`: a point in time, with a nanosecond precision
- `Interval`: a period of time between two instants
- `LocalDate`: an isolated date such as `2014-08-31`
- `LocalDateRange`: an inclusive range of local dates, such as `2014-01-01/2014-12-31`
- `LocalDateTime`: a date-time without a time-zone, such as `2014-08-31T10:15:30`
- `LocalTime`: an isolated time such as `10:15:30`
- `Month`: a month-of-year such as January
- `MonthDay`: a combination of a month and a day, without a year, such as `--12-31`
- `Period`: a date-based amount of time, such as '2 years, 3 months and 4 days'
- `TimeZoneOffset`: an offset-based time-zone, such as `+01:00`
- `TimeZoneRegion`: a region-based time-zone, such as `Europe/London`
- `Year`: a year in the [proleptic calendar](http://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar)
- `YearMonth`: a combination of a year and a month, such as `2014-08`
- `ZonedDateTime`: a date-time with a time-zone, such as `2014-08-31T10:15:30+01:00`.
   This class is conceptually equivalent to the native `DateTime` class

These classes belong to the `Brick\DateTime` namespace.

### Clocks

All objects read the current time from a `Clock` implementation. The following implementations are available:

- `SystemClock`: the default clock, returns the system time
- `FixedClock`: returns a pre-configured time, useful for tests
- `OffsetClock`: adds an offset to another clock

These classes belong to the `Brick\DateTime\Clock` namespace.

In your application, you will most likely never touch the defaults, and always use the default clock:

```php
use Brick\DateTime\LocalDate;
use Brick\DateTime\TimeZone;

echo LocalDate::now(TimeZone::utc()); // 2017-10-04
```

In your tests however, you will probably want to set the time to test your application in known conditions;
in that case, `FixedClock` comes in handy:

```php
use Brick\DateTime\Clock\FixedClock;
use Brick\DateTime\Instant;
use Brick\DateTime\LocalDate;
use Brick\DateTime\TimeZone;

$clock = new FixedClock(Instant::of(1000000000));
echo LocalDate::now(TimeZone::utc(), $clock); // 2001-09-09
```

### Exceptions

The following exceptions can be thrown:

- `Brick\DateTime\DateTimeException` when an illegal operation is performed
- `Brick\DateTime\Parser\DateTimeParseException` when `parse()`ing an invalid string representation
