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

### Cakephp for heroku
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

3. Heroku環境用の app.php を読み込む設定
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
