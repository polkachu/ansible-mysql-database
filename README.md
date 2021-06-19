# Ansible script to securely deploy MySQL server with monitoring

This is an opinionated setup script for a MySQL SERVER. It assumes that you have an want to

- Install MySQL
- Install firewall and expose an alternative port (other than 3306) for MySQL
- Install monitoring tools such as node exporter, process exporter and promtail and feed the data to a monitoring server.

## Prep

Make your own inventory file first.

```bash
cp inventory.sample inventory
```

## Step-By-Step Server Setup

### Install MySQL

```bash
ansible-playbook -i inventory main_mysql.yml
```

This will do the following:

- Set up firewall for MySQL and expose an alternative port other than 3306 default port
- Install MySQL and copy a custom mysqld.cfn file

### Secure MySQL

Log into the database server and do the following:

Run secure mysql installation script

```bash
mysql_secure_installation
```

Log into mysql and create an exporter user

```bash
CREATE USER 'exporter'@'localhost' IDENTIFIED BY '24SJHabe5dc736e7b694a3f8f!' WITH MAX_USER_CONNECTIONS 2;

CREATE USER 'admin'@'%' IDENTIFIED BY '24SJHabe5dc736e7b694a3f8f!';

GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'localhost';

GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';

FLUSH PRIVILEGES;

EXIT;

```

### Prepare the server

```bash
ansible-playbook -i inventory main_monitor.yml
```

This will do the following:

- Set up firewall for node exporter and process exporter
- Install node exporter
- Install process exporter
- Install promtail for logging
