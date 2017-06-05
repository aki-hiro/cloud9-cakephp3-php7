cloud9-cakephp3-php7

initialize

$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt-get update
$ sudo apt-get install libapache2-mod-php7.0
$ sudo a2dismod php5
$ sudo a2enmod php7.0
$ sudo apt-get install php7.0-dom php7.0-mbstring php7.0-zip php7.0-mysql php7.0-intl

..and cakephp3

$ composer create-project --prefer-dist cakephp/app myapp