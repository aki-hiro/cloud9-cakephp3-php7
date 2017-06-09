cloud9-cakephp3-php7

# initialize

$ sudo add-apt-repository ppa:ondrej/php  
$ sudo apt-get update  
$ sudo apt-get install libapache2-mod-php7.0  
$ sudo a2dismod php5  
$ sudo a2enmod php7.0  
$ sudo apt-get install php7.0-dom php7.0-mbstring php7.0-zip php7.0-mysql php7.0-intl

# ..and cakephp3  

$ composer create-project --prefer-dist cakephp/app myapp  

# ..and phpmyadmin intall

$ phpmyadmin-ctl install

# ..and deploy for heroku

### Cakephp setting for heroku
1. composer.json に heroku-buildpack-php を追加する。
```json:composer.json
  "require-dev": {
       "psy/psysh": "@stable",
       "cakephp/debug_kit": "~3.0",
       "cakephp/bake": "~1.0",
       "heroku/heroku-buildpack-php": "*"
   },
```

2. Procfileを作成する。
```Procfile
web: vendor/bin/heroku-php-apache2 webroot/
```

3. Heroku環境用の app.php を読み込む設定 bootstrap.php
```php:bootstrap.php
try {
    Configure::config('default', new PhpConfig());
    Configure::load('app', 'default', false);
} catch (\Exception $e) {
    die($e->getMessage() . "\n");
}
// Load an environment local configuration file.
// You can use a file like app_local.php to provide local overrides to your
// shared configuration.
//Configure::load('app_local', 'default');
if (isset($_ENV['CAKE_ENV'])) {
    Configure::load('app_' . $_ENV['CAKE_ENV'], 'default');
}
```

4. app_heroku.php
```php:app_heroku.php
<?php
$db = parse_url(env('CLEARDB_DATABASE_URL'));
return [
    'debug' => false,
    'Security' => [
        'salt' => env('SALT'),
    ],
    'Datasources' => [
        'default' => [
            'className' => 'Cake\Database\Connection',
            'driver' => 'Cake\Database\Driver\Mysql',
            'persistent' => false,
            'host' => $db['host'],
            'username' => $db['user'],
            'password' => $db['pass'],
            'database' => substr($db['path'], 1),
            'encoding' => 'utf8',
            //'timezone' => 'UTC',
            'cacheMetadata' => true,
            'quoteIdentifiers' => false,
        ],
    ],
    'EmailTransport' => [
        'default' => [
            'className' => 'Smtp',
            // The following keys are used in SMTP transports
            'host' => 'ssl://' . $smtp['host'],
            'port' => $smtp['port'],
            'timeout' => 30,
            'username' => $smtp['user'],
            'password' => $smtp['pass'],
            'client' => null,
            'tls' => null,
        ],
    ],
    'Log' => [
        'debug' => [
            'className' => 'Cake\Log\Engine\ConsoleLog',
            'stream' => 'php://stdout',
            'levels' => ['notice', 'info', 'debug'],
        ],
        'error' => [
            'className' => 'Cake\Log\Engine\ConsoleLog',
            'stream' => 'php://stderr',
            'levels' => ['warning', 'error', 'critical', 'alert', 'emergency'],
        ],
    ],
];
```

## heroku 操作
1. loginから配置まで
```
$ heroku login
$ heroku config:add CAKE_ENV="heroku"
$ heroku config:add SALT="123456789123456"
$ heroku config:add SMTP_SERVER="smtp://username:password@smtp.mail.hoge.jp:465"
$ heroku buildpacks:set https://github.com/heroku/heroku-buildpack-php
$ git push heroku master
```

2. DB使用
herokuのアカウントから追加したappに移動。add-onにClearDbを追加

3. マイグレーション & シード
```
$ heroku run bash
$ bin/cake migrations migrate
$ bin/cake bake seed
```
