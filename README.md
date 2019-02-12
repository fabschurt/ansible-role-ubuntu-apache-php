# Ansible Ubuntu Apache-PHP

[![Travis CI](https://img.shields.io/travis/fabschurt/ansible-role-ubuntu-apache-php/master.svg)](https://travis-ci.org/fabschurt/ansible-role-ubuntu-apache-php)

This role will bootstrap an Apache/PHP deployment environment for websites and
web apps, and is optimized for working with the famous «Capistrano workflow». In
particular, it means that: 1 app = 1 Apache vhost = 1 Linux user, and that this
app will be hosted in this user’s home directory, and that the app’s document
root will be set to `/home/{user_name}/public_html` (this actually being a
symbolic link to `/home/{user_name}/app/current`, which will itself generally be
a symbolic link to `/home/{user_name}/releases/{some_release}`, depending on the
deployment tool you’re using).

## Requirements

* Ubuntu 18.04 remote host(s) with root access
* Ansible >= 2.4

## Role variables

This role is configurable with the following variables:

* `paranoid_mode`: when `true`, enables some «security through obscurity» tricks
  (mainly: it will hide Apache/PHP server signatures from HTTP headers)
* `default_http_port`: the default TCP port that Apache will listen to by default,
  in case you haven’t configured a specific port for a given virtual host
* `deploy_pubkeys`: a list of SSH public keys that can be used to log into any
  app-hosting user account (each item in the array can be either the actual public
  key, or a URL where the key can be found)
* `vhosts`: a list of objects describing the applications that will be hosted
  on the target hosts; each element in this list is an object with the following
  properties (optional properties are marked with a __*__):
    - `name`: the name identifying the application; it will be used as the
      user account name, Apache vhost name, Apache log directory name, etc.
    - `http_port`: the TCP port that Apache will listen to for this virtual host
    - `server_name`: a FQDN that will be used as the value of the `ServerName`
      Apache directive
    - `server_aliases*`: an array of alias FQDNs that will be concatenated as the
      value of the `ServerAlias` Apache directive
    - `additional_config*`: a string containing some literal Apache config that
      will be inserted at the end of the vhost’s Ansible-generated config (you
      should use a YAML [block literal](https://en.wikipedia.org/wiki/YAML#Indented_delimiting)
      string here); caution: this will be inserted inside a `<Directory>` block
      matching the app’s document root, so you can’t use all existing directives
      here

See the [Example playbook](#example-playbook) section below for a reference of
these variables’ default values.

## Example playbook

This is an example playbook that demonstrates how you would use the role,
provided that you’ve [installed](https://galaxy.ansible.com/docs/using/installing.html)
it already. The variable values used here reflect the default values declared in
`defaults/main.yml`:

```yaml
- hosts: …
  roles:
    - role: ../ansible-role-ubuntu-apache-php
      paranoid_mode: true
      default_http_port: 80
      deploy_pubkeys: []
      vhosts: []
```

You can also check out the [test playbook](tests/playbook.yml) in this repository
to get a more realistic example.

## License

This software package is licensed under the [MIT License](https://opensource.org/licenses/MIT).
