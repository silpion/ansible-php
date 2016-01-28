# ansible-lib

Provide common tasks as includable task files, e.g. libs and does not
run any task itself.

## Synopsis

```yaml
- name: Include check-mode detection
  tags: "{{ role_name }}"
  include: "{{ role_path }}/../silpion.lib/tasks/checkmodedetection.yml"
```

```yaml
- name: Include data persistency paradigm
  tags: "{{ role_name }}"
  include: "{{ role_path }}/../silpion.lib/tasks/datapersistency.yml"
```

```yaml
# requires datapersistency.yml

- name: Download some asset
  tags: "{{ role_name }}"
  include: "{{ role_path }}/../silpion.lib/tasks/get_url.yml"
  vars:
    url: "{{ url_variable }}"
    filename: "{{ filename_variable }}"
    sha256sum: "{{ sha256sum_variable }}"
```

```yaml
# requires datapersistency.yml

- name: Upload downloaded asset
  tags: "{{ role_name }}"
  include: "{{ role_path }}/../silpion.lib/tasks/copy.yml"
  vars:
    filename: "{{ filename_variable }}"
```

```yaml
# requires {{ role_path }}/vars/{{ ansible_os_family }}.yml
# respects {{ role_path }}/vars/{{ ansible_distribution }}-{{ ansible_distribution_major_version }}.yml
# respects {{ role_path }}/vars/{{ ansible_distribution }}.yml

- name: Include OS specific configuration
  tags: "{{ role_name }}"
  include: "{{ role_path }}/../silpion.lib/tasks/os-specific-vars.yml"
```

```yaml
# requires {{ role_path }}/vars/versions/{{ role_name_version }}.yml

- name: Include version specific configuration
  tags: "{{ role_name }}"
  include: "{{ role_path }}/../silpion.lib/tasks/version-specific-vars.yml
  vars:
    version: "{{ role_name_version }}"
```

## Configuration

``silpion.lib`` role uses variables from ``silpion.util`` role as
default values for its own variables. If there is no variable from
silpion.util role configured, silpion.lib role uses the same sane
defaults.

See [Role Variables](#role_variables) documentation below.

### Library

The following features/paradigms are available to be used.

#### Data persistency

Download assets once to the local workstation and distribute as often
as required in context of local network.

```yaml
- name: Include data persistency tasks
  tags: "{{ role_name }}"
  include: "{{ role_name }}/../silpion.lib/tasks/datapersistency.yml"
```

By default this installs one directory on the workstation and one on
the managed node.

See [Role Variables](#role_variables) documentation below.

##### Download assets (get_url.yml)

``tasks/get_url.yml`` is basically a wrapper for the Ansible ``get_url``
module using some defaults based on the ``util``/``lib`` configuration,
e.g. ``become`` based privilege escalation with ``local_action``.

Downloads will be stored in ``{{ lib_persistent_data_path_local }}``.

```yaml
- name: Download some assets with silpion.lib/get_url
  tags: "{{ role_name }}"
  include: "{{ role_name }}/../silpion.lib/tasks/get_url.yml"
  vars:
    src: "{{ url }}"
    filename: "{{ filename }}"
```

##### Upload assets (copy.yml)

``tasks/copy.yml`` is basically a wrapper for the Ansible ``copy``
module using some defaults based on the ``util``/``lib`` configuration,
e.g. ``become`` based privilege escalation.

Uploads will be stored in ``{{ lib_persistent_data_path_remote }}``.

```yaml
- name: Upload some assets with silpion.lib/copy
  tags: "{{ role_name }}"
  with_items:
    - filename1
    - filename2
  include: "{{ role_path }}/../silpion.lib/tasks/copy.yml"
  vars:
    filename: "{{ item }}"
```

#### Check mode detection

lib role provides tasks for check mode detection. Including
``checkmodedetection.yml`` provides a boolean run-time fact
``lib_fact_check_mode`` to use ``when`` conditionals with.

```yaml
- name: Include check mode detection
  include: silpion.lib/tasks/checkmodedetection.yml

- name: Run a task when Ansible is NOT in --check mode
  when: not lib_fact_check_mode
  module:
    arg: value
```

## Requirements

* None

## <a name="role_variables"></a>Role Variables

All variables use the corresponding variable from ``silpion.util`` role as
defaults. If there are no variables from silpion.util are configured, the
``|default()`` values are copied from the defaults of silpion.util.

### privilege escalation (local\_action)

* ``lib_local_action_become_enable``: Whether to use privilege escalation for ``local_action`` (boolean, default: ``{{ util_local_action_become_enable|default(false) }}``)
* ``lib_local_action_become_user``: Target user when using privilege escalation for ``local_action`` (string, default: ``{{ util_local_action_become_user|default('root') }}``)
* ``lib_local_action_become_method``: Privilege escalation method when using privilege escalation for ``local_action`` (string, default: ``{{ util_local_action_become_method|default('sudo') }}``)

### privilege escalation (action)

* ``lib_action_become_enable``: Wether to use privilege escaliot for remote actions (boolean, default: ``{{ util_action_become_enable|default(true) }}``)
* ``lib_action_become_user``: Target user when using privilege escalation for remote actions (string, default: ``{{ util_action_become_user|default('root') }}``)
* ``lib_action_become_method``: Privilege escalation method when using privilege escalation for remote actions (string, default: ``{{ util_action_become_method|default('sudo') }}``)

### data persistency

* ``lib_persistent_data_path_local``: Path for downloading assets with tasks/get\_url.yml (string, default: ``{{ util_persistent_data_path_local|default(lookup('env', 'HOME') + '/.ansible/assets') }}`` -> ``$HOME/.ansible/assets``)
* ``lib_persistent_data_path_remote``: Path for uploading assets with tasks/copy.yml (string, default: ``{{ util_persistent_data_path_remote|default('/usr/local/src/ansible/data') }}`` -> ``/usr/local/src/ansible/data``)

### modules configuration

* ``lib_module_get_url_timeout``: Default timeout for the ``get_url`` module when using tasks/get\_url.yml (int, default: ``{{ util_module_get_url_timeout|default(10) }}``)

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

Mark Kusch @silpion.de mark.kusch


<!-- vim: set nofen ts=4 sw=4 et: -->
