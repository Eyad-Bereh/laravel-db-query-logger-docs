# Installation and Configuration

## Installation

To use this package, you can install it using the composer dependency manager by running the command:

```bash
composer require eyadbereh/laravel-db-query-logger
```

***

## Configuration

This package ships with a configuration file that allows you to modify its behavior.

You can do this by publishing the configuration file using the following command:

```bash
php artisan vendor:publish --provider="EyadBereh\\LaravelDbQueryLogger\\LaravelDbQueryLoggerServiceProvider"
```

This command will create a config file called `db-query-logger.php` within your `config` folder.&#x20;

The configuration file looks as follows:

```php
<?php

use EyadBereh\LaravelDbQueryLogger\Drivers\ArrayDriver;
use EyadBereh\LaravelDbQueryLogger\Drivers\JsonFileDriver;
use EyadBereh\LaravelDbQueryLogger\Drivers\LogFileDriver;
use EyadBereh\LaravelDbQueryLogger\Drivers\TelegramDriver;
use EyadBereh\LaravelDbQueryLogger\Drivers\WebhookDriver;
use EyadBereh\LaravelDbQueryLogger\Enums\SqlStatements;

return [
    'enabled' => env('LARAVEL_DB_QUERY_LOGGER_ENABLED', true),

    'environments' => env('LARAVEL_DB_QUERY_LOGGER_ENVIRONMENTS') ? explode(',', env('LARAVEL_DB_QUERY_LOGGER_ENVIRONMENTS')) : null,

    'query_time_threshold' => null,

    'connections' => null, 

    'statements' => [
        SqlStatements::SELECT,
        SqlStatements::INSERT,
        SqlStatements::UPDATE,
        SqlStatements::DELETE,
    ],

    'driver' => env('LARAVEL_DB_QUERY_LOGGER_DRIVER', 'log_file'),

    'drivers' => [
        'log_file' => [
            'concrete' => LogFileDriver::class,
            'path' => storage_path('db-query-logger'),
            'message_format' => '[:datetime:] - [:statement_type:] - [query = :query:] - [bindings = :bindings:] - [time = :time: ms] - [connection = :connection:]',
            'use_laravel_logs' => false,
        ],
        'json_file' => [
            'concrete' => JsonFileDriver::class,
            'path' => storage_path('db-query-logger'),
            'schema' => [
                'datetime' => ':datetime:',
                'statement_type' => ':statement_type:',
                'query' => ':query:',
                'bindings' => ':bindings:',
                'time' => ':time:',
                'connection' => ':connection:',
            ],
        ],
        'array' => [
            'concrete' => ArrayDriver::class,
        ],
        'webhook' => [
            'concrete' => WebhookDriver::class,
            'callback_url' => env('LARAVEL_DB_QUERY_LOGGER_WEBHOOK_CALLBACK_URL'),
            'method' => 'POST',
            'secret' => [
                'header' => 'X-Query-Logger-Token',
                'value' => env('LARAVEL_DB_QUERY_LOGGER_WEBHOOK_TOKEN'),
            ],
            'headers' => [
                // send any additional headers with the request
            ],
            'data' => [
                // send any additional data with the request
            ],
        ],
        'telegram' => [
            'concrete' => TelegramDriver::class,
            'message_format' => 'Datetime: [:datetime:]\nStatement Type:[:statement_type:]\nSQL Query: [:query:]\nBindings: [:bindings:]\nExecution Time: [:time: ms]\nConnection Name: [:connection:]',
            'bot_token' => env('LARAVEL_DB_QUERY_LOGGER_TELEGRAM_BOT_TOKEN'),
            'chat_ids' => env('LARAVEL_DB_QUERY_LOGGER_TELEGRAM_CHAT_IDS') ? explode(',', env('LARAVEL_DB_QUERY_LOGGER_TELEGRAM_CHAT_IDS')) : null,
        ],
    ],
];

```

By default, you don't have to adjust anything here and the package will still perform its job fine. However, if you need to make any modifications, you will need to understand what those keys mean. For ease, the following table explains the functionality of each one.

<table><thead><tr><th width="217" align="center">Key</th><th width="92" align="center">Type </th><th width="140" align="center">Default </th><th align="center">Description</th></tr></thead><tbody><tr><td align="center">enabled</td><td align="center">boolean</td><td align="center"><code>true</code></td><td align="center">Allows you to toggle activate or deactivate the package. This option can also be set through the <code>LARAVEL_DB_QUERY_LOGGER_ENABLED</code> environment variable</td></tr><tr><td align="center">environments</td><td align="center">array of strings</td><td align="center"><code>null</code></td><td align="center">Defines the environments in which the package should work. For example, you may want the package to only work in <code>development</code> environment, or maybe <code>production</code> environment, or maybe both, so you can define an array that contains the names of the environments.<br>An alternative way to control this option would be by passing a comma-separated string of environment names to the <code>LARAVEL_DB_QUERY_LOGGER_ENVIRONMENTS</code> environment variable.</td></tr><tr><td align="center">query_time_threshold</td><td align="center">integer</td><td align="center"><code>null</code></td><td align="center">Defines the minimum required time in milliseconds a query must take to complete execution in order to be logged.<br>Setting this key to be <code>null</code> will cause the package to log all queries.</td></tr><tr><td align="center">connections</td><td align="center">array of strings</td><td align="center"><code>null</code></td><td align="center">Defines the database connections the package should query logs from.<br>Setting this key to be <code>null</code> will cause the package to listen to all database connections.</td></tr><tr><td align="center">statements</td><td align="center">array of enum values</td><td align="center">All values inside the <code>SqlStatements</code> enum</td><td align="center">Defines the type of SQL statements to be logged. For example, setting this key to contain <code>SqlStatements::SELECT</code> Will cause only the <code>SELECT</code> queries to be logged.</td></tr><tr><td align="center">driver</td><td align="center">string</td><td align="center"><code>'log_file'</code></td><td align="center">Defines the used driver for the logging process. Several drivers are predefined inside the package, please take your time to learn about them on the respective documentation page.</td></tr><tr><td align="center">drivers</td><td align="center">array</td><td align="center">All built-in drivers</td><td align="center">Defines options related to each built-in driver. This is also the place to register your custom driver.</td></tr></tbody></table>

