# Installation guide

## Requirements

### On the allspark machine
- Docker ( tested with version 18.04 )

### On the control machine
- Ansible ( tested with version 2.5.5 )

## Ansible playbook

- Edit the file `group_vars/all.yml` to fit with
your needs, like:
  - The `allspark_root_domain` to use your domain name
    (each component will be exposed as a subdomain).
  - Enable or disable component using their `enabled` boolean toggle

!!! note
    You can customize the image or tag for a component by overriding the `component_image` and `component_tag`, using either :

    - [Ansible extra vars](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#passing-variables-on-the-command-line)
    - Add those variables to your `group_vars/all.yml` file.

    _e.g_: (group_vars)
    ```yaml
    # Change gitlab-ce to gitlab-ee
    gitlab_image: gitlab_ee
    gitlab_tag: latest
    ```
    You can access the complete list of available components in the [roles/downloads/defaults/main.yml](https://github.com/TheFkinCompany/allspark/blob/master/roles/download/defaults/main.yml) file.

!!! warning
    For offline install, the images configuration must be the same on both end.

If you are on a migration see the [operation pages](https://github.com/TheFkinCompany/allspark/blob/master/docs/pages/operation.md)

If you are on a migration see the [operation pages](/operation.md)

### Online install

- Change the hosts file to point to the allspark machine.

```bash
ansible-playbook -i hosts install.yml
```

At this point, you will be able to see component show up along with their access URL
at `ingress.YOUR_ROOT_DOMAIN`.

### Offline install
#### 0. On the online control machine

!!! note
    Change the `allspark_release_destination` and `allspark_release_tmp_directory` to point to somwhere with at least 10Go of free space

```bash tab="Bash"
ansible-playbook -i hosts release.yml
```

```ini tab="hosts"
[all]
localhost ansible_connection=local
```

it will generate a `.tar.gz` file at `allspark_release_destination`.
This file needs to be copied along with the `group_vars`
directory to the offline allspark machine.

#### 1. On the offline allspark machine

- Import the `group_vars` directory and the relase `.tar.gz`
- Set the `allspark_release_destination` in the `group_vars/all.yml` file to point to the release.
- Set the `allspark_release_tmp_directory` to point to somwhere with at least 10Go of free space

```bash tab="Bash"
ansible-playbook -i hosts setup.yml
ansible-playbook -i hosts install.yml
```

```ini tab="hosts"
[all]
localhost ansible_connection=local
```

!!! warning
    This playbook does not setup the system yet. The release is packaged along with
    CentOS RPM dependencies as well as pip wheels, but you will however have to install them yourself.
