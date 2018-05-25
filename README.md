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
| `openio_redis_failover_timeout` | `180000` | Specifies the failover timeout in milliseconds |
| `openio_redis_loglevel` | `notice` | Specify the server verbosity level |
| `openio_redis_master` | `dict` | IP `address` and `port` of master |
| `openio_redis_master_groupname` | `"{{ openio_redis_namespace }}-master-1"` | Set of instances |
| `openio_redis_maxclients` | `10000` | Set the max number of connected clients at the same time |
| `openio_redis_maxmemory` | `0` | Set a memory usage limit to the specified amount of bytes |
| `openio_redis_mode` | `server` | The redis mode : `server` or `sentinel` |
| `openio_redis_mode_details` | `dict` | Dict of `port` and `service_name` for a `openio_redis_mode` |
| `openio_redis_namespace` | `"OPENIO"` | Namespace |
| `openio_redis_parallel_sync` | `1` | How many slaves we can reconfigure to point to the new slave simultaneously during the failover |
| `openio_redis_pid_directory` | `"/run/redis/{{ openio_redis_namespace }}/{{ service_type }}-{{ openio_redis_serviceid }}"` | Folder for pid file |
| `openio_redis_quorum` | `2` | The quorum is the number of `sentinel` that need to agree about the fact the master is not reachable, in order for really mark the slave as failing, and eventually start a fail over procedure if possible |
| `openio_redis_saves` | `dict` | Will save the DB if both the given number of seconds and the given number of write operations against the DB occurred |
| `openio_redis_serviceid` | `"0"` | ID in gridinit |
| `openio_redis_stacktrace_size_limit` | `8192` | Stacktrace maximum size |
| `openio_redis_tcp_backlog_queue_size` | `511` | In high requests-per-second environments you need an high backlog in order to avoid slow clients connections issues |
| `openio_redis_tcp_keepalive` | `0` | The specified value (in seconds) is the period used to send ACKs to clients |
| `openio_redis_timeout` | `0` | Close the connection after a client is idle for N seconds |
| `openio_redis_volume` | `"/var/lib/oio/sds/{{ openio_redis_namespace }}/{{ service_type }}-{{ openio_redis_serviceid }}"` | The DB will be written inside this directory |

## Dependencies

No dependencies.

## Example Playbook

```yaml
- hosts: redis
  become: true
  vars:
    NS: OIO
    interface: 'eth0'
  roles:
    - role: repository
    - role: gridinit
      openio_gridinit_namespace: "{{ NS }}"
      
    - role: redis
      openio_redis_namespace: "{{ NS }}"
      openio_redis_bind_interface: "{{ interface }}"
      openio_redis_master:
        address: "{{ hostvars[ groups['redis'][0] ]['ansible_' + interface ]['ipv4']['address'] }}"
        port: 6011
    
    - role: redis
      openio_redis_mode: sentinel
      openio_redis_namespace: "{{ NS }}"
      openio_redis_bind_interface: "{{ interface }}"
      openio_redis_master:
        address: "{{ hostvars[ groups['redis'][0] ]['ansible_' + interface ]['ipv4']['address'] }}"
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

Apache License, Version 2.0

## Contributors

- [Cedric DELGEHIER](https://github.com/cdelgehier) (maintainer)
