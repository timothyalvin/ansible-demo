# Ansible Role: MariaDB

[![Build Status](https://travis-ci.org/tschifftner/ansible-role-mariadb.svg?branch=master)](https://travis-ci.org/tschifftner/ansible-role-mariadb)

Installs MariaDB on Debian/Ubuntu linux servers.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```
mariadb_version: '10.3'
```

### Create database users

_Passwords are required!_

```
mariadb_group_users:
  - name: 'user1'
    password: '*******'
    priv: '*.*:USAGE'
    hosts:
      - localhost
      - 127.0.0.1

  - name: 'normaltravis'
    password: '*******'
    priv: '*.*:ALL,GRANT'
    hosts:
      - localhost
      - 127.0.0.1
      
  - name: 'old'
    state: absent
    hosts:
      - localhost
      - 127.0.0.1
```

### Create databases

All databases need to be defined in ```mariadb_databases```:

```
mariadb_host_databases:
  - name: 'testdb'
    collation: utf8_general_ci
    encoding: utf8
```

### Settings user priviledges on databases and tables

To define user priviledges use the following format:
```
db.table:priv1,priv2
```

Idempotent solution for multiple priviledges (@see http://stackoverflow.com/a/22959760)

```
mysql_privileges:
  - db1.*:ALL,GRANT
  - db2.*:USAGE
  
mariadb_host_users:  
  - name: 'user1'
    password: 'travis'
    priv={{ mysql_privileges|join('/') }}
    hosts:
      - localhost
      - 127.0.0.1
```

### Available variables for default, hosts and groups
```
mariadb_default_users: []
mariadb_host_users: []
mariadb_group_users: []

mariadb_default_databases: []
mariadb_host_databases: []
mariadb_group_databases: []
```

### Security

This role sets an administrative user and removes root entirely. Please define the following settings:

```
mariadb_admin_home: '/root'
mariadb_admin_user: 'admin'
mariadb_admin_password: 'Set strong password here!'
```

_When a custom admin username is used, a password must be set!_

## Dependencies

None.

## Example Playbook

    - hosts: server
      roles:
        - { role: tschifftner.mariadb }

## Root password lost

[If you cannot login anymore you can reset your credentials.](https://falseisnotnull.wordpress.com/2012/10/31/did-you-lose-your-mariadb-root-password-gnulinux/)

## Remove User
To remove a user define ```state: absent```
```
mariadb_host_users:
   - name: 'test'
     host: localhost
     password: 'test'
     priv: '*.*:ALL'
     state: 'absent'
```

## Replication

### Master/Master Replication

_Server #1 (db1.example.org):_
```
mariadb_server_id: 1 #Must Be 1
mariadb_replication_role: 'master'
mariadb_replication_master: 'db2.example.org'
mariadb_replication_username: 'replicationuser'
mariadb_replication_password: 'strong-password'
```

_Server #2 (db2.example.org):_
```
mariadb_server_id: 2
mariadb_replication_role: 'master'
mariadb_replication_master: 'db1.example.org'
mariadb_replication_username: 'replicationuser'
mariadb_replication_password: 'strong-password'
```

### Master/Slave Replication

Not possible

## Supported OS

 - Debian 9 (Stretch)
 - Debian 8 (Jessie)
 - Ubuntu 18.04 (Bionic Beaver)
 - Ubuntu 16.04 (Xenial Xerus)
 
## Supported MariaDB versions

 - 10.2
 - 10.3
 
## Required ansible version

Ansible 2.5+

## Upgrade MariaDB 10.2 to 10.3

https://mariadb.com/kb/en/library/upgrading-from-mariadb-102-to-mariadb-103/

## License

[MIT License](http://choosealicense.com/licenses/mit/)

## Author Information

 - [Tobias Schifftner](https://twitter.com/tschifftner), [ambimax® GmbH](https://www.ambimax.de)