# Log File Driver

## Introduction

The `LogFileDriver` provides a simple way of logging your database queries by storing the logs in a log file, just like the file used by the Laravel framework to log errors and warnings.

## Technical Details

When used, this driver creates a new log file each day to store the logs and the log file name is in the format `Y-m-d.log`

By default, logs are stored within a sub-directory called `db-query-logger` inside your `storage` directory, but this option can be changed by adjusting the value of the config option `drivers.log_file.path` inside the package configuration file.

The log entries are stored according to a predefined format, you can adjust this format by editing the value of the config option `drivers.log_file.message_format` .

In case you don't want to store logs in separate files, you can change the config option `drivers.log_file.use_laravel_logs` to be `true` , in this case, the package will write these logs to your `laravel.log` file using the `notice` logging level as defined by the [PSR-3](https://www.php-fig.org/psr/psr-3/) standard.

