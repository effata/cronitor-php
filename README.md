# Unofficial PHP Cronitor.io Monitor Caller Client

[![Build Status](https://travis-ci.org/nathanielks/cronitor-io-php.svg?branch=master)](https://travis-ci.org/nathanielks/cronitor-io-php)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/nathanielks/cronitor-io-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/nathanielks/cronitor-io-php/?branch=master)
[![Code Coverage](https://scrutinizer-ci.com/g/nathanielks/cronitor-io-php/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/nathanielks/cronitor-io-php/?branch=master)
[![StyleCI](https://styleci.io/repos/51043597/shield)](https://styleci.io/repos/51043597)

## Table of Contents

+ [Installation](#installation)
+ [Requirements](#requirements)
+ [Examples](#examples)

## Installation

Using [composer](https://packagist.org/packages/nathanielks/cronitor-io-php):

```bash
$ composer require nathanielks/cronitor-io-php
```

## Requirements

The following versions of PHP are supported by this version.

+ PHP 5.4
+ PHP 5.5
+ PHP 5.6
+ PHP 7.0

## Examples

You can use `Cronitor\Client` by itself:

```php
$monitorId = 'id of the monitor found on the cronitor dashboard';
$client = new Cronitor\Client($monitorId);
$client->run();
// do some stuff...
$client->complete();
```

You can also use the included `cronitorMonitorTask` function. It will wrap the provided function in `run()/complete()` and catch and then rethrow any Exceptions thrown in the process. By default, it will send a message to Cronitor's `/fail` endpoint with the message generated by the Exception.

```php
$monitorId = 'id of the monitor found on the cronitor dashboard';
$client = new Cronitor\Client($monitorId);

try{
  cronitorMonitorTask(
    $client,
    function(){
      // do some stuff...
    }
  );
} catch (Exception $e){
  // If there are any errors thrown, cronitorMonitorTask will catch the
  // exception and use the error message as the message to send to Cronitor's
  // /fail endpoint. It will then rethrow the exception to be caught and
  // processed here

  // Do some other stuff, like logging perhaps?
}
```

You can also provide a function to return a message to send to Cronitor:

```php
$monitorId = 'id of the monitor found on the cronitor dashboard';
$client = new Cronitor\Client($monitorId);

try{
  cronitorMonitorTask(
    $client,
    function(){
      // do some stuff...
    },
    function($exception, $client){
      return array(
        'msg' => 'This will be the error message sent to Cronitor.io'
      );
    }
  );
} catch (Exception $e){
  // Do some other stuff, like logging perhaps?
}
```

The expected return value is an array with `msg` and `pause` as keys. `msg` is
a string that will be url-encoded and `pause` is an integer representing the
number of hours to pause alerts on this particular monitor.

Interested in returning something from the closure? You sure can!

```php
$monitorId = 'id of the monitor found on the cronitor dashboard';
$client = new Cronitor\Client($monitorId);

try{
  $someValue = cronitorMonitorTask(
    $client,
    function(){
      // do some stuff...
      return 'heck yeah you can!';
    }
  );
} catch (Exception $e){
  // Do some other stuff, like logging perhaps?
}
```

Enjoy!
