rackspace_openssh Cookbook
================
Installs and configures openssh.


Requirements
------------
### Platform
- Debian/Ubuntu
- RHEL/CentOS


Recipes
-------
### default
Selects the packages to install by package name and manages the sshd service.


Usage
-----
Ensure that the openssh packages are installed and the service is managed with `recipe[rackspace_openssh]`.


Attributes List
---------------
The attributes list is dynamically generated, and lines up with the default openssh configs.

This means anything located in [sshd_config](http://www.openbsd.org/cgi-bin/man.cgi?query=sshd_config&sektion=5) or [ssh_config](http://www.openbsd.org/cgi-bin/man.cgi?query=sshd_config&sektion=5) can be used in your node attributes.

* If the option can be entered more then once, use an _Array_, otherwise, use a _String_. If the option is host-specific use a `Hash` (please see below for more details).
* The current default attributes are opinated `ssh_config` and `sshd_config` provided by openssh.
* The namespace for `sshd_config` is `node['rackspace_openssh']['templates_cookbook']['server']`.
* Likewise, the namespace for `ssh_config` is `node['rackspace_openssh']['templates_cookbook']['client']`.
* An attribute can be an `Array`, a `Hash` or a `String`.
* If it is an `Array`, each item in the array will get it's own line in the config file.
* `Hash` attributes are meant to used with `ssh_config` namespace to create host-specific configurations. The keys of the `Hash` will be used as the `Host` entries and their associated entries as the configuration values.
* All the values in openssh are commented out in the `attributes/default.rb` file for a base starting point.


Dynamic ListenAddress
---------------------
Pass in a `Hash` of interface names, and IP address type(s) to bind sshd to. This will expand to a list of IP addresses which override the default `node['rackspace_openssh']['templates_cookbook']['server']['listen_address']` value.


Examples and Common usage
-------------------------
These can be mixed and matched in roles and attributes.  Please note, it is possible to get sshd into a state that it will not run.  If this is the case, you will need to login via an alternate method and debug sshd like normal.

####  Bind to a specific set of address (this example actually binds to all).

Not to be used with `node['rackspace_openssh']['templates_cookbook']['listen_interfaces']`.

```json
"rackspace_openssh": {
  "server": {
    "address_family": "any",
      "listen_address": [ "192.168.0.1", "::" ']
    }
  }
}
```

### Bind to the addresses tied to a set of interfaces.

```json
"rackspace_openssh": {
  "listen_interfaces": {
    "eth0": "inet",
    "eth1": "inet6"
  }
}
```

### Host-specific configurations with hashes.
You can use a `Hash` with `node['rackspace_openssh']['templates_cookbook']['client']` to configure different values for different hosts.

```json
"client": {
  "*": {
    "g_s_s_a_p_i_authentication": "yes",
    "send_env": "LANG LC_*",
    "hash_known_hosts": "yes"
  },
  "localhost": {
    "user_known_hosts_file": "/dev/null",
    "strict_host_key_checking": "no"
  },
  "127.0.0.1": {
    "user_known_hosts_file": "/dev/null",
    "strict_host_key_checking": "no"
  },
  "other*": {
    "user_known_hosts_file": "/dev/null",
    "strict_host_key_checking": "no"
  }
}
```

The keys are used as values with the `Host` entries. So, the configuration fragment shown above generates:

```text
Host *
SendEnv LANG LC_*
HashKnownHosts yes
GSSAPIAuthentication yes
Host localhost
StrictHostKeyChecking no
UserKnownHostsFile /dev/null
Host 127.0.0.1
StrictHostKeyChecking no
UserKnownHostsFile /dev/null
Host other*
StrictHostKeyChecking no
UserKnownHostsFile /dev/null
```


License & Authors
-----------------
- Author:: Adam Jacob <adam@opscode.com>
- Author:: Ted Neykov <ted.neykov@rackspace.com>
- Author:: Ryan Richard <ryan.richard@rackspace.com>

```text
Copyright:: 2008-2009, Opscode, Inc
Copyright:: 2014, Rackspace, US Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
