# ansible-lib

Provide common tasks as includable task files, e.g. libs.

## Synopsis

The silpion.lib role provides functionality commonly used in other
roles.

## Description

The silpion.lib role is a non-callable role which is designed to
have single files from tasks/ included in other roles.

### Configuration

It uses all variables from ``silpion.util`` role as defaults while
providing its own variables to override

### Library

The following features/paradigms are available to be used.

#### Data persistency

Download assets once to the local workstation and distribute as often
as required in context of local network.

```yaml
- name: Include data persistency tasks
  include: silpion.lib/tasks/datapersistency.yml
```

By default this installs one directory on the workstation and one on
the managed node. The following defaults apply the directories created:


## Requirements

* None

## Role Variables

* ``variable_name``: Variable description (<!variable type>, default: ``variable default value``)

## Contributing

If you want to contribute to this repository please be aware that this
project uses a [gitflow](http://nvie.com/posts/a-successful-git-branching-model/)
workflow with the next release branch called ``next``.

Please fork this repository and create a local branch split off of the ``next``
branch and create pull requests back to the origin ``next`` branch.

## License

Apache Version 2.0

## Integration testing

This role provides integration tests using the Ruby RSpec/serverspec framework
with a few drawbacks at the time of writing this documentation.

Running integration tests requires a number of dependencies being
installed. As this role uses Ruby RSpec there is the need to have
Ruby with rake and bundler available.

```shell
# install role specific dependencies with bundler
bundle install
```

<!-- -->

```shell
# run the complete test suite with Docker
rake suite
```

<!-- -->

```shell
# run the complete test suite with Vagrant
source  envvars-vagrant.sample
rake suite

# run the complete test suite with Vagrant without destroying the box afterwards
source  envvars-vagrant.sample
RAKE_ANSIBLE_VAGRANT_DONT_CLEANUP=1 rake suite
```


## Author information

<!Author Name> @<!email_prefix> <!email_suffix>


<!-- vim: set nofen ts=4 sw=4 et: -->
