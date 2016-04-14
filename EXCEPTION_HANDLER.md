# Exception Handler with Response Builder #

Properly designed REST API should never hit consumer with plain string, HTML page nor anything but JSON.
While in regular use this is quite easy to achieve, unexpected problems like uncaught exception or
even enabled maintenance mode can still happen and pollute returned data of many APIs world wide.
Do not be one of them, and take care of that in advance with couple of easy steps. In Laravel, unexpected
situations are routed to Exception Handler. Default implementation is not JSON API friendly, therefore
Response Builder package provides drop-in replacement. Once installed, JSON response will still be
generated.


## Planting Response Builder's Exception Handler ##

First edit your `ErrorCode` class (that one which stores your error codes) and add the following
constants, **assigning unique codes within your allowed code range**:

    const UNKNOWN_METHOD = ...;
    const SERVICE_IN_MAINTENANCE = ...;
    const HTTP_EXCEPTION = ...;
    const UNCAUGHT_EXCEPTION = ...;

Then, edit `config/response_builder.php` file and map codes to error messages by adding following
lines to your `map` array:

	ErrorCode::UNKNOWN_METHOD         => 'response-builder::builder.unknown_method',
	ErrorCode::SERVICE_IN_MAINTENANCE => 'response-builder::builder.service_in_maintenance',
	ErrorCode::HTTP_EXCEPTION         => 'response-builder::builder.http_exception_fmt',
	ErrorCode::UNCAUGHT_EXCEPTION     => 'response-builder::builder.uncaught_exception_fmt',

In the same config file edit `exception_handler` entry to make it look like this (if you follow
this guide strictly, just uncomment these lines):

	'exception_handler' => [
		'unknown_method'         => ErrorCode::UNKNOWN_METHOD,
		'service_in_maintenance' => ErrorCode::SERVICE_IN_MAINTENANCE,
		'http_exception'         => ErrorCode::HTTP_EXCEPTION,
		'uncaught_exception'     => ErrorCode::UNCAUGHT_EXCEPTION,
	],

Finally edit `app/Exceptions/Handler.php` file, remove **all** its content and make it look like this:

    <?php
    namespace App\Exceptions;

    use MarcinOrlowski\ResponseBuilder\ResponseBuilderExceptionHandler as ExceptionHandler;

    class Handler extends ExceptionHandler {}


## Using own messages ##

The above links codes with Response Builder built-in messages, but you can use any strings you want, by
providing keys matching your own messages. For `RESPONSE_BUILDER_UNCAUGHT_EXCEPTION` and
`RESPONSE_BUILDER_HTTP_EXCEPTION` codes `:message` placeholder can be used in corresponding messages
which will be substituted by actual exception message when used.


## Notes ##

The above assumes you keep your codes in `ErrorCode` class stored in `app/ErrorCode.php` and using `App\ErrorCode` namespace.