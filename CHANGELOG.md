# 2.0.1

Anja Siek (1):

* fix requironments and README

# 2.0.0

Anja Siek (2):

* add code from ansible-role generator; update tasks ans requironments so that role can run independent
* beautifying

Mark Kusch (4):

* Apply more style-guide rules
* Fix unnecessary changed event in register for php5enmod command
* Fixup acpu module name in test playbook
* Fix unnecessary changed events in Debian php5{en,dis}mod commands


# 1.1.1

Conor Schaefer (1):

* Check for definedness of php\_register\_mods\_ini var

# 1.1.0

Anja Siek (2):

* remove useless variable
* add RedHat support

# 1.0.0

Anja Siek (5):

* add refresh after ppa add
* add crudini install via pip
* fix wording
* only do update if ppa changed
* add cli package

# 0.4.0

Anja Siek (3):

* add modifications to fix the php module-ini files so that they are valid for ini\_file module
* add php5disable task to allow disabling of modules
* add better handling for the decision between debian php >= 5.4 module management
* fix conditions for registered variable
* fix registered varname

# 0.3.0

Anja Siek (6):

* add php5enmod for php-versions >= 5.4 becouse https://github.com/silpion/ansible-php/issues/1
* fix Typo and rename variable
* change version-management for Ubuntu
* change version-management for Ubuntu
* fix merge Conflict
* remove unused variable php\_ppa\_purge, add default for section in php ini, beautifying

# 0.2.0

Mark Kusch (2):

* ansible-php 0.2.0
* Fix unneccessary whitespace

# ...
