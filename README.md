# ansible-lib

Provide common tasks as includable task files, e.g. libs and does not
run any task itself.

# Synopsis

```yaml
- name: Include check-mode detection
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/checkmodedetection.yml"
```

```yaml
- name: Include data persistency paradigm
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/datapersistency.yml"
```

```yaml
# requires datapersistency.yml

- name: Download some asset
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/get_url.yml"
  vars:
    url: "{{ url_variable }}"
    filename: "{{ filename_variable }}"
    checksum: "{{ checksum_variable }}"
```

```yaml
# requires datapersistency.yml

- name: Upload downloaded asset
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/copy.yml"
  vars:
    filename: "{{ filename_variable }}"
```

```yaml
# requires {{ role_path }}/vars/{{ ansible_os_family }}.yml
# respects {{ role_path }}/vars/{{ ansible_distribution }}-{{ ansible_distribution_major_version }}.yml
# respects {{ role_path }}/vars/{{ ansible_distribution }}.yml

- name: Include OS specific configuration
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/os-specific-vars.yml"
```

```yaml
# requires {{ role_path }}/vars/versions/{{ role_name_version }}.yml

- name: Include version specific configuration
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/version-specific-vars.yml
  vars:
    version: "{{ role_name_version }}"
```

```yaml
- name: Include local facts installation
  tags: "{{ role_name }}"
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/localfacts.yml
  vars:
    template: myrolesfactstemplate.j2
    namespace: myroleshortname
```

# Assumption

Assumption on using silpion.lib is that roles for a playbook are installed
in a directory called `roles` beneath `playbook.yml`. Otherwise defaults/
fallbacks when including os-specific or version-specific variables might
fail unrelated.

## role-aware includes

Ansible is currently working on role-aware includes, like:

```yaml
- name: Include file from silpion.lib role
  tags: "{{ role_name }}"
  include:
    role: silpion.lib
    file: datapersistency.yml
```

As soon as this is available, `silpion.lib` will have marked TODOs
addressed and there shouldn't be assumptions anymore.

# Configuration

``silpion.lib`` role uses variables from ``silpion.util`` role as
default values for its own variables. If there is no variable from
silpion.util role configured, silpion.lib role uses the same sane
defaults.

See [Role Variables](#role_variables) documentation below.

# Library

The following features/paradigms are available to be used.

## Data persistency

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

### Vars

* None

### Download assets (get_url.yml)

``tasks/get_url.yml`` is basically a wrapper for the Ansible ``get_url``
module using some defaults based on the ``util``/``lib`` configuration,
e.g. ``become`` based privilege escalation with ``local_action``.

Downloads will be stored in ``{{ lib_persistent_data_path_local }}``.

**NOTE**: With Ansible 2.1 the `sha256sum` argument got deprecated.
`sha256sum` is replaced with `checksum` in `silpion.lib >= 2.1.N`
and requires the new format of `algorithm:checksum`.

```yaml
- name: Download some assets with silpion.lib/get_url
  tags: "{{ role_name }}"
  include: "{{ role_name }}/../silpion.lib/tasks/get_url.yml"
  vars:
    src: "{{ url }}"
    filename: "{{ filename }}"
```

#### Vars

See ``ansible-doc get_url`` for a more in-depth documentation of
module related configuration options.

##### Mandatory

* ``url``: Download URL.
* ``filename``: Filename of the downloaded asset.

##### Optional

* ``no_log``: Activate ``no_log: true`` for a download task (default: ``omit``)
* ``url_username``: Username for authenticated services (default: ``omit``)
* ``url_password``: Password for authenticated services (default: ``omit``)
* ``checksum``: `algorithm:checksum` (default: ``omit``)
* ``force``: Force overriding local assets with a download (default: ``omit``)
* ``timeout``: Connection timeout (default: ``{{ lib_module_get_url_timeout }}`` -> ``10``)
* ``use_proxy``: Whether to use the system proxy configuration (default: ``true``)
* ``validate_certs``: Whether to validate SSL certificates (default: ``true``)
* ``mode``: Filesystem access mode for downloaded asset (default: ``0644``)
* ``owner``: Owner for the downloaded asset (default: ``{{ util_persistent_data_path_local_owner|default(omit) }}``)
* ``group``: Group for the downloaded asses (default: ``{{ util_persistent_data_path_local_group|default(omit) }}``)

### Upload assets (copy.yml)

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
  include: "{{ playbook_dir }}/roles/silpion.lib/tasks/copy.yml"
  vars:
    filename: "{{ item }}"
```

#### Vars

See ``ansible-doc copy`` for a more in-depth documentation of
module related configuration options.

##### Mandatory

* ``filename``: Filename of the downloaded asset to upload.

##### Optional

* ``backup``: Whether to create a backup copy (default: ``false``)
* ``follow``: Whether to follow symbolic links (default: ``false``)
* ``force``: Whether to force override existing remote files (default: ``true``)
* ``validate``: Command to validate upload (default: ``omit``)
* ``mode``: Filesystem access mode for the uploaded asset (default: ``0644``)
* ``owner``: Owner for the uploaded asset (default: ``{{ util_persistent_data_path_remote_owner|default(omit) }}``)
* ``group``: Group for the uploaded asset (default: ``{{ util_persistent_data_path_remote_group|default(omit) }}``)

### Check mode detection

lib role provides tasks for check mode detection. Including
``checkmodedetection.yml`` provides a boolean run-time fact
``lib_fact_check_mode`` to use ``when`` conditionals with.

```yaml
- name: Include check mode detection
  tags: "{{ role_name }}"
  include: "{{ role_name }}/../silpion.lib/tasks/checkmodedetection.yml

- name: Run a task when Ansible is NOT in --check mode
  tags: "{{ role_name }}"
  when: not lib_fact_check_mode
  module:
    arg: value
```

### Local facts installation

``tasks/localfacts.yml`` will ensure availability of a directory to
store local facts into, deploy roles local facts based on a template
to be provided into this tasks file and re-read local facts based on
changed events when the template has been deployed.

```yaml
- name: Include local facts installation
  tags: "{{ role_name }}"
  include: "{{ role_name }}/../silpion.lib/tasks/localfacts.yml"
  vars:
    template: "{{ role_name_fact_template }}.j2"
    namespace: myroleshortname
```

#### Vars

##### Mandatory

* ``namespace``: Namespace in ``ansible_local`` to be created (default: ``undefined|mandatory``)

##### Optional

* ``template``: Template to deploy (default: ``facts.j2``)

# Requirements

* None

# <a name="role_variables"></a>Role Variables

All variables use the corresponding variable from ``silpion.util`` role as
defaults. If there are no variables from silpion.util are configured, the
``|default()`` values are copied from the defaults of silpion.util.

## privilege escalation (local\_action)

* ``lib_local_action_become_enable``: Whether to use privilege escalation for ``local_action`` (boolean, default: ``{{ util_local_action_become_enable|default(false) }}``)
* ``lib_local_action_become_user``: Target user when using privilege escalation for ``local_action`` (string, default: ``{{ util_local_action_become_user|default('root') }}``)
* ``lib_local_action_become_method``: Privilege escalation method when using privilege escalation for ``local_action`` (string, default: ``{{ util_local_action_become_method|default('sudo') }}``)

## privilege escalation (action)

* ``lib_action_become_enable``: Wether to use privilege escaliot for remote actions (boolean, default: ``{{ util_action_become_enable|default(true) }}``)
* ``lib_action_become_user``: Target user when using privilege escalation for remote actions (string, default: ``{{ util_action_become_user|default('root') }}``)
* ``lib_action_become_method``: Privilege escalation method when using privilege escalation for remote actions (string, default: ``{{ util_action_become_method|default('sudo') }}``)

## data persistency

* ``lib_persistent_data_path_local``: Path for downloading assets with tasks/get\_url.yml (string, default: ``{{ util_persistent_data_path_local|default(lookup('env', 'HOME') + '/.ansible/assets') }}`` -> ``$HOME/.ansible/assets``)
* ``lib_persistent_data_path_remote``: Path for uploading assets with tasks/copy.yml (string, default: ``{{ util_persistent_data_path_remote|default('/usr/local/src/ansible/data') }}`` -> ``/usr/local/src/ansible/data``)

## modules configuration

* ``lib_module_get_url_timeout``: Default timeout for the ``get_url`` module when using tasks/get\_url.yml (int, default: ``{{ util_module_get_url_timeout|default(10) }}``)

## play configuration

* ``lib_roles_path``: Default path to play roles directory where for example lib role is installed (string, default: ``{{ playbook_dir }}/roles``)

## local facts

Ansible ``setup`` module supports ``fact_path`` variable. This can be
configured with a variable from util role.

* ``util_local_facts_directory``: Where to store local facts to (string, default: ``/etc/ansible/facts.d``)

# Contributing

If you want to contribute to this repository please be aware that this
project uses a [gitflow](http://nvie.com/posts/a-successful-git-branching-model/)
workflow with the next release branch called ``next``.

Please fork this repository and create a local branch split off of the ``next``
branch and create pull requests back to the origin ``next`` branch.

# License

Apache Version 2.0

# Integration testing

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

# Author information

Mark Kusch @silpion.de mark.kusch


<!-- vim: set nofen ts=4 sw=4 et: -->
