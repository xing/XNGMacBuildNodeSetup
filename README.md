# XNGMacBuildNodeSetup

These are the ansible playbooks we use to configure our Mac machines for usage with Jenkins CI.
They are used as nodes and need to install the following tools:

* [Homebrew](http://brew.sh)
* [Ruby](https://www.ruby-lang.org)
* [ios-sim](https://github.com/phonegap/ios-sim) (for use with [ISRakel](https://github.com/xing/israkel))
* [Java](https://www.java.com/en/) (because JNLP needs Java to connect to our master Jenkins)

So as you can guess we have one master (Linux) which the Mac OS X nodes connect to via JNLP.

Setup of the master is __not__ covered by this repository.

In addition to installing tools it also does the following:

* Installs Xcode using [Xcode::Install](https://github.com/neonichu/xcode-install)
* Configures [monit](http://mmonit.com/monit/) which monitors the JNLP process and restarts it if goes down.

## Installing ansible

To install ansible simply run:

```bash
$ brew install ansible
```

## Running the playbook
### 1. Add your nodes:

You can cluster nodes, e.g. `[macpros]`. Each node gets a name, we usually use `macpro<ip_address>` and an `ansible_ssh_host` which is the actual IP address of this node.

```yaml
# inventory
[macpros]
macpro1 ansible_ssh_host=191.168.178.23
macpro2 ansible_ssh_host=191.168.178.24
```

### 2. Configure the node specific variables

__Please note that each host has its own configuration in its own folder.__

```yaml
# host_vars/macpro1/main.yml
---
jnlp_url: http://ci.example.com/computer/macpro1/slave-agent.jnlp
jnlp_secret: kfldsfjkhf12hroi32urz4h3o4öti43zthi # Actual JNLP secret
xcode_version: "6.3"
```

### 3. Configure the global variables

```yaml
# osx.yml
hosts: macpros # This is the name your gave your cluster in the inventory file
...
vars:
  - home_dir: /Users/jenkins
  - jenkins_home_dir: /Users/jenkins/Jenkins
  - jenkins_url: http://ci.example.com
...
```

```yaml
# roles/monit/vars/main.yml
monit_path: /Users/jenkins/Jenkins/monit
```

```yaml
# roles/xcode/vars/main.yml
...
deliver_user: ... # Your Apple Dev center user
deliver_password: ... # Your Apple Dev center password
```

### 3. Run the playbook

This will ssh into each node and execute all the steps defined in `osx.yml`

```bash
$ ansbile-playbook osx.yml --ask-sudo-pass
```

## Contact

XING AG

* https://github.com/xing
* https://twitter.com/xingdevs
* https://dev.xing.com

## License

Released under the MIT license. See the LICENSE file for more info.
