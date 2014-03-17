# ansible PHP

Install and Configure PHP and Submodules

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

set special configs for modules (conf.d) If you want to change something you need to set all 

Example:
```
php_modules_conf:
  - name: pdo
    configs:
    - option: "extension"
      value: "pdo.so"
      section: "pdo"
```

### php_version: none

set php - Version, Choices: 

* php54 
* php55

default is 'none', this will use the OS - default 


## Dependencies
 None

## License
Apache Version 2.0

## Author Information
Anja Siek @anja.siek silpion.de
