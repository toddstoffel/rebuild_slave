![logo](mdb_logo.jpg)

# Automatic Replica Creation Utility For MariaDB

## Description
This playbook will automatically create/rebuild and configure a replication slave. It can be run from any host that can access your database servers via SSH. This is designed to work on both MariaDB and MariaDB ColumnStore systems.

On the **master**, it will:

1. Launch [MariaBackup](https://mariadb.com/kb/en/library/mariabackup-overview/).
1. Create a streaming snapshot of your data directory using [xbstream/mbstream](https://www.percona.com/doc/percona-xtrabackup/2.3/xbstream/xbstream.html).
1. Compress the stream with [pigz](https://zlib.net/pigz/).
1. Stream to the slave via [socat](http://www.dest-unreach.org/socat/).

On the **slave**, it will:
1. Receive the stream.
1. Decompress.
1. Prepare the backup.
1. Find the GTID or binary log position.
1. Register with the master.
1. Start slave replication.

#### Prerequisites

* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)
* [Git](https://git-scm.com/downloads)
* Ports **4444** and **3306** must be open between your master and slave.
* Port 22 must be open to your servers from whatever machine you run the playbook from.
* Create replication user following this example:
```
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'idbrep'@'10.%' IDENTIFIED BY 'C0lumnStore!';
```

#### Setup

* `git clone https://github.com/toddstoffel/rebuild_slave.git`
* `cd` to cloned folder
* Edit the [inventory/hosts](inventory/hosts) file with your own information
* Edit the [inventory/group_vars/all.yml](inventory/group_vars/all.yml) file with your own information
* Run `ansible-playbook create_slave.yml`
