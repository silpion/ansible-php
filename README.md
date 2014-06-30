# ansible PHP

This role can install and configure php and php-submodules on Ubuntu. 
For special configuration it will create a seperate config-file in conf.d directory, 
so your config-changes will be executed with all php-instances on your system (cli, apache, fpm ...)
Also you can override special modules config like apc.ini. 

## Variables

### php_package_list_additional: []

list of additional packages to install

Example:

```
php_package_list_additional:
  - php5-gd
  - php5-mcrypt
  - php5-curl
  ...
```
### php_config_changes_ini: "test.ini"

set name of ini-file for PHP-config changes set in "php_config". 
This will be located in conf.d . 
Example:

```
 php_config_changes_ini: "test.ini"
```
will create (in Debian) /etc/php5/conf.d/test.ini


### php_config: []

set php config Variables:

Example:

```
php_config:
  - option: "date.timezone"
    section: "Date"
    value: "UTC"
```
### php_modules_conf: []

set special configuration for modules (conf.d) .
you can set special name like apc-my-project will create apc-my-project.ini 
so you can simply add special module configurations. 
If you want to override config you need to know how file is named on system. ("apc.ini" set name: "apc")


Example:

```
php_modules_conf:
  - name: apc-my-project
    configs:
    - option: "apc.enabled"
      value: "1"
      section: "apc"
```

### php_version: none

set php - Version, Choices: 

* php54 
* php55

default is 'none', this will use the OS - default 

### php_mods_enabled: []
add modules to enable (for php >= 5.4 ).
This is needed if you use "php_config" or "php_modules_conf" variable in special inifiles. 
Use this filename without ".ini" as modulename

Example:

```
php_mods_enabled: 
 - test
 - apc-my-project

```


## Dependencies
 None

## License
Apache Version 2.0

## Author Information
Anja Siek @anja.siek silpion.de
