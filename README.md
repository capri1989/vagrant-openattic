# openattic-vagrant

This repository contains configuration files that simplify the setup of the development environment to work on [openATTIC](http://openattic.org) with a [ceph](https://ceph.com/) cluster managed by [DeepSea](https://github.com/SUSE/DeepSea).

| VM  |  IP | Roles | Description |
|----------| ----------|----------| ----------|
| `salt` | 192.168.100.200 | **master**, **admin** |Run [openattic-docker](https://github.com/openattic/openattic-docker) container or openattic (salt-master + salt-minion)|
| `node1` | 192.168.100.201 | **mon**, **igw**, **rgw**, **mgr** | Run ceph (salt-minion) |
| `node2` | 192.168.100.202 | **mon**, **igw**, **ganesha**, **mgr** | Run ceph (salt-minion) |
| `node3` | 192.168.100.203 | **mon**, **rgw**, **ganesha** | Run ceph (salt-minion) |

## Requirements

* [Vagrant](https://www.vagrantup.com/)
* Local copy of the [openATTIC repository](https://bitbucket.org/openattic/openattic)
* Local copy of the [DeepSea repository](https://github.com/SUSE/DeepSea)

## Setup

Configuration resides in the `settings.yml` file that contains the custom configuration to spin up the cluster. See
[`settings.sample.yml`](settings.sample.yml) for an example of the `settings.yml` that you must create.
