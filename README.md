# ansible PHP

This role can install and configure php and php-submodules on Ubuntu or Debian. 
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

set php config Variables. Default section is 'PHP'.

Example:

```
php_config:
  - option: "date.timezone"
    section: "Date"
    value: "UTC"
  - option: "safe_mode"
    value: "Off"
```
### php_modules_conf: []

set special configuration for modules (conf.d) .
If you want to override config you need to know how file is named on system. 
For "apc.ini" set name: "apc" this will make changes in apc.ini


Example:

```
php_modules_conf:
  - name: apc
    configs:
    - option: "apc.enabled"
      value: "1"
      section: "apc"
```

### php_mods_enabled: []

add modules to enable (for php >= 5.4 ).
Use this with the ini-filename without ".ini" as modulename

Example:

```
php_mods_enabled:
 - test
 - apc

```

### php_mods_disabled: []

disable php modules (for php >= 5.4 ). 
Use this with the ini-filename without ".ini" as modulename

Example:

```
php_mods_disabled:
 - curl

```

### php_ppa: 

only for Ubuntu: you can set ppa to install different PHP-Versions from given ppa

Example:

```
php_ppa: "ppa:ondrej/php5-5.6"
```

## Dependencies
 None

## License
Apache Version 2.0

## Author Information
Anja Siek @anja.siek silpion.de


<!-- vim: set nofen ts=4 sw=4: -->
