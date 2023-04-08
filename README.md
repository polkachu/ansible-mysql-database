# Ansible script to securely deploy MySQL server with monitoring

This is an opinionated setup script for a MySQL SERVER. It assumes that you have an want to

- Install MySQL
- Install firewall and expose an alternative port (other than 3306) for MySQL
- Install monitoring tools such as node exporter, process exporter and promtail and feed the data to a monitoring server.

## Prep

Make your own inventory file first.

```bash
cp inventory.sample inventory.ini
```

## Step-By-Step Server Setup

### Install the server monitor services

```bash
ansible-playbook -i inventory main_server_monitor.yml
```

This will do the following:

- Set up firewall for node exporter and process exporter
- Install node exporter
- Install process exporter
- Install promtail for logging

### Install MySQL

```bash
ansible-playbook -i inventory main_mysql.yml
```

This will do the following:

- Set up firewall for MySQL and expose an alternative port other than 3306 default port
- Install MySQL with a custom mysqld.cfn file

### Secure MySQL

Log into the database server and do the following:

Run secure mysql installation script

```bash
mysql_secure_installation
```

Create an admin user and an exporter user

```bash
CREATE USER 'exporter'@'localhost' IDENTIFIED BY 'xxxxxxxxxxxxxxx' WITH MAX_USER_CONNECTIONS 2;

CREATE USER 'admin'@'%' IDENTIFIED BY 'xxxxxxxxxxxxxxx';

GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'localhost';

GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';

FLUSH PRIVILEGES;

EXIT;
```

### Install MySQL monitoring service

```bash
ansible-playbook -i inventory main_mysql_monitor.yml
```
