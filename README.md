[![Build Status](https://travis-ci.org/open-io/ansible-role-openio-redis.svg?branch=master)](https://travis-ci.org/open-io/ansible-role-openio-redis)
# Ansible role `redis`

An Ansible role for redis. Specifically, the responsibilities of this role are to:

- Install a Redis
- Configure a redis server
- Configure a redis sentinel

## Requirements

- Ansible 2.4+

## Role Variables


| Variable   | Default | Comments (type)  |
| :---       | :---    | :---             |
| `openio_redis_auth_pass` | `""` | Set the password to use to authenticate with the master and slaves |
| `openio_redis_bind_address` | `"{{ hostvars[inventory_hostname]['ansible_' + openio_redis_bind_interface]['ipv4']['address'] }}"` | The address that this redis instance will run on |
| `openio_redis_bind_interface` | `"{{ ansible_default_ipv4.alias }}"` | The interface that this redis instance will run on |
| `openio_redis_databases` | `16` | Set the number of databases |
| `openio_redis_down_after` | `1000` | Number of milliseconds the master (or any attached slave or sentinel) should be unreachable |
openio_redis_inventory_groupname
| `openio_redis_inventory_groupname` | `redis` | Set your inventory groupname |
| `openio_redis_loglevel` | `notice` | Specify the server verbosity level |
| `openio_redis_master` | `dict` | IP `address` and `port` of master |
| `openio_redis_master_groupname` | `"{{ openio_redis_namespace }}-master-1"` | Set of instances |
| `openio_redis_maxclients` | `10000` | Set the max number of connected clients at the same time |
| `openio_redis_maxmemory` | `0` | Set a memory usage limit to the specified amount of bytes |
| `openio_redis_type` | `redis` | The redis mode : `redis` or `redissentinel` |
| `openio_redis_type_details` | `dict` | Dict of `port` and `service_name` for a `openio_redis_type` |
| `openio_redis_namespace` | `"OPENIO"` | Namespace |
| `openio_redis_pid_directory` | `"/run/oio/sds/{{ openio_redis_namespace }}"` | Folder for pid file |
| `openio_redis_quorum` | `2` | The quorum is the number of `sentinel` that need to agree about the fact the master is not reachable, in order for really mark the slave as failing, and eventually start a fail over procedure if possible |
| `openio_redis_saves` | `dict` | Will save the DB if both the given number of seconds and the given number of write operations against the DB occurred |
| `openio_redis_serviceid` | `"{{ 0 + openio_legacy_serviceid | d(0) | int }}"` | ID in gridinit |
| `openio_redis_stacktrace_size_limit` | `8192` | Stacktrace maximum size |
| `openio_redis_tcp_backlog_queue_size` | `511` | In high requests-per-second environments you need an high backlog in order to avoid slow clients connections issues |
| `openio_redis_tcp_keepalive` | `0` | The specified value (in seconds) is the period used to send ACKs to clients |
| `openio_redis_timeout` | `0` | Close the connection after a client is idle for N seconds |
| `openio_redis_volume` | `"/var/lib/oio/sds/{{ openio_redis_namespace }}/{{ openio_redis_type }}-{{ openio_redis_serviceid }}"` | The DB will be written inside this directory |
| `openio_redis_package_upgrade` | `false` | Set the packages to the latest version (to be set in extra_vars) |

## Dependencies

No dependencies.

## Example Playbook

```yaml
- hosts: redis
  become: true
  vars:
    NS: OIO
  roles:
    - role: users
    - role: repository
    - role: gridinit
      openio_gridinit_namespace: "{{ NS }}"
      openio_gridinit_per_ns: true
    - role: role_under_test
      openio_redis_namespace: "{{ NS }}"
      openio_redis_bind_address: "{{ ansible_default_ipv4.address }}"
      openio_redis_master:
        address: 172.17.0.2
        port: 6011

    - role: role_under_test
      openio_redis_namespace: "{{ NS }}"
      openio_redis_bind_address: "{{ ansible_default_ipv4.address }}"
      openio_redis_type: redissentinel
      openio_redis_bind_port: 6012
      openio_redis_master:
        address: 172.17.0.2
        port: 6011
```


```ini
[redis]
node1 ansible_host=192.168.1.173
node2 ansible_host=192.168.1.174
node3 ansible_host=192.168.1.175
```

## Contributing

Issues, feature requests, ideas are appreciated and can be posted in the Issues section.

Pull requests are also very welcome.
The best way to submit a PR is by first creating a fork of this Github project, then creating a topic branch for the suggested change and pushing that branch to your own fork.
Github can then easily create a PR based on that branch.

## License

GNU AFFERO GENERAL PUBLIC LICENSE, Version 3

## Contributors

- [Cedric DELGEHIER](https://github.com/cdelgehier) (maintainer)
