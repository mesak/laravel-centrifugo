<p align="center">Documentation <b>EN</b> | <a href="https://github.com/denis660/laravel-centrifuge/blob/master/README_RU.md">RU</a></p>

<h1 align="center">Laravel + Centrifugo</h1>
<h2 align="center">Centrifugo broadcast driver for Laravel 8.75.0 - 11 </h2>

<p align="center">
<a href="https://scrutinizer-ci.com/g/denis660/laravel-centrifugo"><img src="https://scrutinizer-ci.com/g/denis660/laravel-centrifugo/badges/quality-score.png?b=master" alt="Build Status"></a>
<a href="https://github.com/denis660/laravel-centrifugo/releases"><img src="https://img.shields.io/github/release/denis660/laravel-centrifugo.svg?style=flat-square" alt="Latest Version"></a>
<a href="https://scrutinizer-ci.com/g/denis660/laravel-centrifugo"><img src="https://img.shields.io/scrutinizer/g/denis660/laravel-centrifugo.svg?style=flat-square" alt="Quality Score"></a>
<a href="https://github.styleci.io/repos/324202212"><img src="https://github.styleci.io/repos/324202212/shield?branch=master" alt="StyleCI"></a>
<a href="https://packagist.org/packages/denis660/laravel-centrifugo"><img src="https://img.shields.io/packagist/dt/denis660/laravel-centrifugo.svg?style=flat-square" alt="Total Downloads"></a>
<a href="https://github.com/denis660/Centrifuge/blob/master/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="Software License"></a>
</p>

## Introduction
Centrifugo broadcaster for laravel  , based on:
- [LaraComponents/centrifugo-broadcaster](https://github.com/LaraComponents/centrifugo-broadcaster)
- [centrifugal/phpcent](https://github.com/centrifugal/phpcent)

## Features
- Compatible with latest [Centrifugo v5.4.5](https://github.com/centrifugal/centrifugo/releases/tag/v5.4.5) 🚀
- Wrapper over [Centrifugo HTTP API](https://centrifugal.dev/docs/server/server_api) 🔌
- Authentication with JWT token (HMAC algorithm) for anonymous, authenticated user and private channel 🗝️

## Requirements
- PHP >= 7.4 , 8.0, 8.1, 8.2, 8.3
- Laravel 8.75.0 - 11.0
- Guzzlehttp/Guzzle 6 - 7
- Centrifugo Server v5.4.5 or newer (see [here](https://github.com/centrifugal/centrifugo))

## Installation
By default, broadcasting is not enabled in new Laravel 11 applications. You may enable broadcasting using the install:broadcasting Artisan command:
```bash
php artisan install:broadcasting
```
The `install:broadcasting` command will create the config/broadcasting.php configuration file. In addition, the command will create the `routes/channels.php` file where you may register your application's broadcast authorization routes and callbacks.


Require this package with composer:

```bash
composer require denis660/laravel-centrifugo
```

```bash
php artisan centrifuge:install
```


Application Credentials
In order to establish a connection to Reverb, a set of Reverb "application" credentials must be exchanged between the client and server. These credentials are configured on the server and are used to verify the request from the client. You may define these credentials using the following environment variables:

Also you should add these two lines to your .env file:

```
CENTRIFUGO_TOKEN_HMAC_SECRET_KEY=token_hmac_secret_key-from-centrifugo-config
CENTRIFUGO_API_KEY=api_key-from-centrifugo-config
CENTRIFUGO_URL=http://localhost:8000
```

These lines are optional:
```
CENTRIFUGO_SSL_KEY=/etc/ssl/some.pem
CENTRIFUGO_VERIFY=false
```

Don't forget to change `BROADCAST_DRIVER` setting in .env file!

```
BROADCAST_DRIVER=centrifugo
```

## Basic Usage

To configure Centrifugo server, read [official documentation](https://centrifugal.dev)

For broadcasting events, see [official documentation of laravel](https://laravel.com/docs/8.x/broadcasting)

A simple client usage example:

```php
<?php
declare(strict_types = 1);

namespace App\Http\Controllers;


use denis660\Centrifugo\Centrifugo;
use Illuminate\Support\Facades\Auth;

class ExampleController
{

    public function example(Centrifugo $centrifugo)
    {
        // Send message into channel
        $centrifugo->publish('news', ['message' => 'Hello world']);

        // Generate connection token
        $token = $centrifugo->generateConnectionToken((string)Auth::id(), 0, [
            'name' => Auth::user()->name,
        ]);

        // Generate private channel token
        $apiSign = $centrifugo->generatePrivateChannelToken((string)Auth::id(), 'channel', time() + 5 * 60, [
            'name' => Auth::user()->name,
        ]);

        //Get a list of currently active channels.
        $centrifugo->channels();

        //Get channel presence information (all clients currently subscribed on this channel).
        $centrifugo->presence('news');

    }
}
```

### Available methods

| Name | Description |
|------|-------------|
| publish(string $channel, array $data, $skipHistory = false) | Send message into channel. |
| broadcast(array $channels, array $data, $skipHistory = false) | Send message into multiple channel. |
| presence(string $channel) | Get channel presence information (all clients currently subscribed on this channel). |
| presenceStats(string $channel) | Get channel presence information in short form (number of clients).|
| history(string $channel, $limit = 0, $since = [], $reverse = false) | Get channel history information (list of last messages sent into channel). |
| historyRemove(string $channel) | Remove channel history information.
| subscribe(string $channel,  string $user, $client = '') | subscribe user from channel. |
| unsubscribe(string $channel, string $user, string $client = '') | Unsubscribe user from channel. |
| disconnect(string $user_id) | Disconnect user by it's ID. |
| channels(string $pattern = '') | Get channels information (list of currently active channels). |
| info() | Get stats information about running server nodes. |
| generateConnectionToken(string $userId = '', int $exp = 0, array $info = [], array $channels = [])  | Generate connection token. |
| generatePrivateChannelToken(string $client, string $channel, int $exp = 0, array $info = []) | Generate private channel token. |

## License

The MIT License (MIT). Please see [License File](https://github.com/denis660/laravel-centrifugo/blob/master/LICENSE for more information.
