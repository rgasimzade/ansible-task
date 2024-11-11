# Ansible Project to Deploy WordPress and MySQL in Docker

This project uses Ansible to deploy a WordPress application and a MySQL database, each running in Docker containers, on separate virtual machines. The setup will install Docker, configure a MySQL database, and set up a WordPress site connected to that database.

## What It Does

This project:
1. **Installs Docker** on two virtual machines (VMs).
2. **Deploys MySQL** in a Docker container on the database server.
3. **Deploys WordPress** in a Docker container on the web server and configures it to use the MySQL database.

## Prerequisites

- Ansible installed on the machine from which you'll run the playbook.
- Two virtual machines (VMs) with SSH access (one for the database and one for the web server).
- Both VMs should be able to communicate with each other over the network (required for WordPress to connect to MySQL).
- Docker will be installed automatically on both VMs.

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository_url>
cd wordpress_project
```

### 2. Configure the Inventory File

The `inventory.ini` file defines the target VMs. Open this file and replace the placeholders with your actual IP addresses and SSH user information:

```ini
[web]
webserver ansible_host=<webserver_ip> ansible_user=<user>

[db]
dbserver ansible_host=<dbserver_ip> ansible_user=<user>
```

- **`<webserver_ip>`**: IP address of the web server (where WordPress will be deployed).
- **`<dbserver_ip>`**: IP address of the database server (where MySQL will be deployed).
- **`<user>`**: The SSH user that has sudo privileges on both VMs.

### 3. Modify MySQL and WordPress Configuration

In the `roles/database_conf/tasks/main.yml` file, you can change the default MySQL configuration:

```yaml
env:
  MYSQL_ROOT_PASSWORD: rootpassword
  MYSQL_DATABASE: wordpress
  MYSQL_USER: wordpressuser
  MYSQL_PASSWORD: wordpresspass
```

Change the following environment variables to match your desired configuration:

- **`MYSQL_ROOT_PASSWORD`**: The root password for MySQL.
- **`MYSQL_DATABASE`**: The name of the WordPress database.
- **`MYSQL_USER`**: The MySQL user that WordPress will use.
- **`MYSQL_PASSWORD`**: The password for the `MYSQL_USER`.

In the `roles/wordpress_conf/tasks/main.yml` file, make sure the database connection settings are correct:

```yaml
env:
  WORDPRESS_DB_HOST: "<dbserver_ip>:3306"
  WORDPRESS_DB_NAME: wordpress
  WORDPRESS_DB_USER: wordpressuser
  WORDPRESS_DB_PASSWORD: wordpresspass
```

- **`WORDPRESS_DB_HOST`**: Replace `<dbserver_ip>` with the IP address of your MySQL server.
- **`WORDPRESS_DB_NAME`**, **`WORDPRESS_DB_USER`**, **`WORDPRESS_DB_PASSWORD`**: These should match the MySQL credentials set above.

### 4. Run the Playbook

Once everything is configured, you can run the playbook to deploy the infrastructure:

```bash
ansible-playbook -i inventory.ini playbook.yml
```

### 5. Access the WordPress Site

After the playbook completes, you should be able to access your WordPress site by navigating to the web server's IP address in your browser.

For example:

```
http://<webserver_ip>
```

### 6. Optional: Modifying Docker Configuration

If you need to customize Docker settings or port mappings, you can modify the `docker_container` settings in the `roles/database_conf/tasks/main.yml` and `roles/wordpress_conf/tasks/main.yml` files.

For instance, you can change the port bindings or container restart policies according to your requirements.

---

## Core Files Overview

- **`inventory.ini`**: Defines target VMs for the web and database servers. Replace `<webserver_ip>` and `<dbserver_ip>` with actual IPs.
- **`playbook.yml`**: Main playbook that installs Docker, sets up MySQL on the database server, and WordPress on the web server.
- **`roles/docker_setup/tasks/main.yml`**: Installs Docker on both servers.
- **`roles/database_conf/tasks/main.yml`**: Sets up MySQL in Docker, pulls the image, and configures the container with environment variables.
- **`roles/wordpress_conf/tasks/main.yml`**: Sets up WordPress in Docker and links it to MySQL using the defined connection settings.
---

## Conclusion

This Ansible project automates the deployment of a WordPress website and a MySQL database running in Docker containers on separate virtual machines. It is fully customizable, so feel free to modify the configuration files to suit your needs.

