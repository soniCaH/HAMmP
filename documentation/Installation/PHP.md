# PHP

## Install multiple PHP version
We want an environment where we can switch between the current active PHP 
versions. This are 5.6.x, 7.0.x and 7.1.x.

This describes how to setup Apache with multiple PHP versions.
You can optionally [install old PHP versions](PHP-old-versions.md).

To do so we install PHP 5.6 > 7.1. Feel free to leave out the versions you 
don't need.

```bash
$ brew install -v homebrew/php/php56
$ brew unlink php56
$ brew install -v homebrew/php/php70
$ brew unlink php70
$ brew install -v homebrew/php/php71
$ brew unlink php71
```

## Configure PHP
Set timezone and change other PHP settings (sudo is needed here to get the 
current timezone on OS X) to be more developer-friendly, and add a PHP error 
log (without this, you may get Internal Server Errors if PHP has errors to 
write and no logs to write to):

We update:

*	`date.timezone` : to the timezone defined in your laptop.
*	`memory_limit` : 512MB
*	`post_max_size` : 200M (needed for large uploads)
*	`upload_max_filesize` : 100M (needed for large uploads)
*	`default_socket_timeout` : 600
*	`max_execution_time` : 30
*	`max_input_time` : 600
*	`error_log` : /Volumes/webdev/www/_apache/log/php-error_log


We need to update the config for each PHP version:

#### PHP 5.6
```bash
$ sed -i '-default' -e 's|^;\(date\.timezone[[:space:]]*=\).*|\1 \"'$(sudo systemsetup -gettimezone|awk -F"\: " '{print $2}')'\"|; s|^\(memory_limit[[:space:]]*=\).*|\1 512M|; s|^\(post_max_size[[:space:]]*=\).*|\1 200M|; s|^\(upload_max_filesize[[:space:]]*=\).*|\1 100M|; s|^\(default_socket_timeout[[:space:]]*=\).*|\1 600|; s|^\(max_execution_time[[:space:]]*=\).*|\1 30|; s|^\(max_input_time[[:space:]]*=\).*|\1 600|; $a\'$'\n''\'$'\n''; PHP Error log\'$'\n''error_log = /Volumes/webdev/www/_apache/log/php56-error.log'$'\n' $(brew --prefix)/etc/php/5.6/php.ini
```

#### PHP 7.0
```bash
$ sed -i '-default' -e 's|^;\(date\.timezone[[:space:]]*=\).*|\1 \"'$(sudo systemsetup -gettimezone|awk -F"\: " '{print $2}')'\"|; s|^\(memory_limit[[:space:]]*=\).*|\1 512M|; s|^\(post_max_size[[:space:]]*=\).*|\1 200M|; s|^\(upload_max_filesize[[:space:]]*=\).*|\1 100M|; s|^\(default_socket_timeout[[:space:]]*=\).*|\1 600|; s|^\(max_execution_time[[:space:]]*=\).*|\1 30|; s|^\(max_input_time[[:space:]]*=\).*|\1 600|; $a\'$'\n''\'$'\n''; PHP Error log\'$'\n''error_log = /Volumes/webdev/www/_apache/log/php70-error.log'$'\n' $(brew --prefix)/etc/php/7.0/php.ini
```

#### PHP 7.1
```bash
$ sed -i '-default' -e 's|^;\(date\.timezone[[:space:]]*=\).*|\1 \"'$(sudo systemsetup -gettimezone|awk -F"\: " '{print $2}')'\"|; s|^\(memory_limit[[:space:]]*=\).*|\1 512M|; s|^\(post_max_size[[:space:]]*=\).*|\1 200M|; s|^\(upload_max_filesize[[:space:]]*=\).*|\1 100M|; s|^\(default_socket_timeout[[:space:]]*=\).*|\1 600|; s|^\(max_execution_time[[:space:]]*=\).*|\1 30|; s|^\(max_input_time[[:space:]]*=\).*|\1 600|; $a\'$'\n''\'$'\n''; PHP Error log\'$'\n''error_log = /Volumes/webdev/www/_apache/log/php71-error.log'$'\n' $(brew --prefix)/etc/php/7.1/php.ini
```



###	Pear & Pecl
Fix a pear and pecl permissions problem:

#### PHP 5.6
```bash
$ chmod -R ug+w $(brew --prefix php56)/lib/php
```

#### PHP 7.0
```bash
$ chmod -R ug+w $(brew --prefix php70)/lib/php
```

#### PHP 7.1
```bash
$ chmod -R ug+w $(brew --prefix php71)/lib/php
```


### Install OpCache
The optional Opcache extension will speed up your PHP environment 
dramatically, so let's install it. Then, we'll bump up the opcache memory limit:

#### PHP 5.6
```bash
$ brew link php56
$ brew install -v php56-opcache
$ /usr/bin/sed -i '' "s|^\(\;\)\{0,1\}[[:space:]]*\(opcache\.enable[[:space:]]*=[[:space:]]*\)0|\21|; s|^;\(opcache\.memory_consumption[[:space:]]*=[[:space:]]*\)[0-9]*|\1256|;" $(brew --prefix)/etc/php/5.6/php.ini
```

#### PHP 7.0
```bash
$ brew unlink php56 && brew link php70
$ brew install -v php70-opcache
$ /usr/bin/sed -i '' "s|^\(\;\)\{0,1\}[[:space:]]*\(opcache\.enable[[:space:]]*=[[:space:]]*\)0|\21|; s|^;\(opcache\.memory_consumption[[:space:]]*=[[:space:]]*\)[0-9]*|\1256|;" $(brew --prefix)/etc/php/7.0/php.ini
```

#### PHP 7.1
```bash
$ brew unlink php70 && brew link php71
$ brew install -v php71-opcache
$ /usr/bin/sed -i '' "s|^\(\;\)\{0,1\}[[:space:]]*\(opcache\.enable[[:space:]]*=[[:space:]]*\)0|\21|; s|^;\(opcache\.memory_consumption[[:space:]]*=[[:space:]]*\)[0-9]*|\1256|;" $(brew --prefix)/etc/php/7.1/php.ini
```



### Install Intl extension
Composer & Symfony2 recommends the installation of the PHP Intl extension:

#### PHP 5.6
```bash
$ brew unlink php71 && brew link php56
$ brew install php56-intl
```

#### PHP 7.0
```bash
$ brew unlink php56 && brew link php70
$ brew install php70-intl
```

#### PHP 7.1
```bash
$ brew unlink php70 && brew link php71
$ brew install php71-intl
```


## Restart Apache
Finaly we can start Apache:

```bash
$ brew services stop httpd24
$ brew services start httpd24
```


## Start also PHP-FPM

```bash
$ brew services start php71
```


## Test
You should now be able to run PHP scripts:

*	http: http://localhost/phpinfo.php
*	https: https://localhost/phpinfo.php


## Switch between PHP versions
The `/Volumes/Webdev/www/_apache/bin` directory of the HAMmP repository contains
a helper script to switch between the different PHP versions:

```
$ sphp 56
$ sphp 70
$ sphp 71
```




---
* [Next : Xdebug](./Xdebug.md)
* [Install older PHP versions](./PHP-old-versions.md)
* [Overview](../README.md)