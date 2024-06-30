Kafka-php
==========

[![tests](https://github.com/g41797/kafka-php/actions/workflows/tests.yml/badge.svg)](https://github.com/g41797/kafka-php/actions/workflows/tests.yml)

Forked from [kafka-php](https://github.com/weiboad/kafka-php).

 Reason of fork
 - non-compatible libraries
 - old PHP version(7.1)

  Kafka-php is a pure PHP kafka client that currently supports greater than 0.8.x version of Kafka, this project v0.2.x and v0.1.x are incompatible if using the original v0.1.x You can refer to the document 
[Kafka PHP v0.1.x Document](https://github.com/weiboad/kafka-php/blob/v0.1.6/README.md), but it is recommended to switch to v0.2.x . v0.2.x use PHP asynchronous implementation and kafka broker interaction, more stable than v0.1.x efficient, because the use of PHP language so do not compile any expansion can be used to reduce the access and maintenance costs


## Requirements

* Minimum PHP version: 8.0
* Kafka version greater than 0.8
* The consumer module needs kafka broker version  greater than 0.9.0

## Installation

The package could be installed with composer:

```shell
composer require g41797/kafka-php
```

## Configuration

Configuration properties are documented in [Configuration](docs/Configure.md)

## Producer

### Asynchronous mode

```php
<?php
require '../vendor/autoload.php';
date_default_timezone_set('PRC');
use Monolog\Logger;
use Monolog\Handler\StdoutHandler;
// Create the logger
$logger = new Logger('my_logger');
// Now add some handlers
$logger->pushHandler(new StdoutHandler());

$config = \Kafka\ProducerConfig::getInstance();
$config->setMetadataRefreshIntervalMs(10000);
$config->setMetadataBrokerList('10.13.4.159:9192');
$config->setBrokerVersion('1.0.0');
$config->setRequiredAck(1);
$config->setIsAsyn(false);
$config->setProduceInterval(500);
$producer = new \Kafka\Producer(
    function() {
        return [
            [
                'topic' => 'test',
                'value' => 'test....message.',
                'key' => 'testkey',
            ],
        ];
    }
);
$producer->setLogger($logger);
$producer->success(function($result) {
	var_dump($result);
});
$producer->error(function($errorCode) {
		var_dump($errorCode);
});
$producer->send(true);
```

### Synchronous mode

```php
<?php
require '../vendor/autoload.php';
date_default_timezone_set('PRC');
use Monolog\Logger;
use Monolog\Handler\StdoutHandler;
// Create the logger
$logger = new Logger('my_logger');
// Now add some handlers
$logger->pushHandler(new StdoutHandler());

$config = \Kafka\ProducerConfig::getInstance();
$config->setMetadataRefreshIntervalMs(10000);
$config->setMetadataBrokerList('127.0.0.1:9192');
$config->setBrokerVersion('1.0.0');
$config->setRequiredAck(1);
$config->setIsAsyn(false);
$config->setProduceInterval(500);
$producer = new \Kafka\Producer();
$producer->setLogger($logger);

for($i = 0; $i < 100; $i++) {
    $producer->send([
        [
            'topic' => 'test1',
            'value' => 'test1....message.',
            'key' => '',
        ],
    ]);
}
```

## Consumer

```php
<?php
require '../vendor/autoload.php';
date_default_timezone_set('PRC');
use Monolog\Logger;
use Monolog\Handler\StdoutHandler;
// Create the logger
$logger = new Logger('my_logger');
// Now add some handlers
$logger->pushHandler(new StdoutHandler());

$config = \Kafka\ConsumerConfig::getInstance();
$config->setMetadataRefreshIntervalMs(10000);
$config->setMetadataBrokerList('10.13.4.159:9192');
$config->setGroupId('test');
$config->setBrokerVersion('1.0.0');
$config->setTopics(['test']);
//$config->setOffsetReset('earliest');
$consumer = new \Kafka\Consumer();
$consumer->setLogger($logger);
$consumer->start(function($topic, $part, $message) {
	var_dump($message);
});
```

## Low-Level API

Refer [Example](https://github.com/weiboad/kafka-php/tree/master/example)

