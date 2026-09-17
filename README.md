# LAMP Stack Deployment on AWS EC2

> Hands-on deployment of a Linux, Apache, MySQL, and PHP (LAMP) environment on AWS EC2 — from provisioning the server to configuring each layer and verifying the full stack works end to end.

![AWS](https://img.shields.io/badge/AWS-EC2-orange)
![Linux](https://img.shields.io/badge/Linux-Ubuntu-E95420)
![Apache](https://img.shields.io/badge/Apache-Web%20Server-D22128)
![MySQL](https://img.shields.io/badge/MySQL-Database-4479A1)
![PHP](https://img.shields.io/badge/PHP-Runtime-777BB4)

## Project Overview

I deployed a traditional **LAMP stack** on an AWS EC2 Ubuntu server, covering the full path from provisioning cloud infrastructure to configuring each application tier and verifying that the components work together.

### Stack

| Layer                 | Technology   | Purpose                       |
| --------------------- | ------------ | ----------------------------- |
| Cloud                 | AWS EC2      | Compute infrastructure        |
| Operating System      | Ubuntu Linux | Server operating environment  |
| Web Server            | Apache       | Handles HTTP requests         |
| Application Runtime   | PHP          | Executes server-side PHP code |
| Database              | MySQL        | Relational database service   |
| Remote Administration | SSH          | Secure server administration  |

## Architecture

                         Internet
                            │
                            │ HTTP :80
                            ▼
                  ┌─────────────────────┐
                  │       AWS EC2       │
                  │    Ubuntu Server    │
                  │                     │
                  │  ┌────────────────┐ │
                  │  │     Apache     │ │
                  │  │   Web Server   │ │
                  │  └───────┬────────┘ │
                  │          ▼          │
                  │  ┌────────────────┐ │
                  │  │      PHP       │ │
                  │  │    Runtime     │ │
                  │  └───────┬────────┘ │
                  │          ▼          │
                  │  ┌────────────────┐ │
                  │  │     MySQL      │ │
                  │  │    Database    │ │
                  │  └────────────────┘ │
                  └─────────────────────┘
                            ▲
                            │ SSH :22
                            │
                      Administrator

## 1. Provisioning the EC2 Instance

I launched an Ubuntu EC2 instance (`t3.micro`) on AWS and connected to it remotely using SSH with a private key pair generated during instance setup.

The initial server preparation included updating and upgrading installed packages:

sudo apt update
sudo apt upgrade

## 2. Apache Web Server

I installed Apache, enabled it to start automatically, verified the service was active, and confirmed that it was serving HTTP requests.

sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl status apache2
curl http://localhost:80

Apache was tested locally using `curl` before being accessed externally through a browser.

## 3. MYSQL Database

I installed MySQL, enabled the service, and used `mysql_secure_installation` to apply the available security-hardening options.

sudo apt install mysql-server -y
sudo systemctl enable --now mysql
sudo mysql_secure_installation
sudo mysql -p

Authenticated access to MySQL was then verified.

> Security note:Credentials used during the original setup are not included in this repository.

## 4. PHP Runtime

I installed PHP together with the Apache PHP module and MySQL integration package.

sudo apt install php libapache2-mod-php php-mysql -y
php -v

This allowed Apache to process PHP files and provided PHP-to-MySQL connectivity.

## 5. Apache Virtual Host & Document Root

Rather than using Apache's default site, I created a dedicated document root and custom virtual host for the project.

DocumentRoot: /var/www/projectlamp
Config file: /etc/apache2/sites-available/projectlamp.conf

The virtual host configuration was:

<VirtualHost \*:80>
ServerName projectlamp
ServerAlias www.projectlamp
ServerAdmin webmaster@localhost
DocumentRoot /var/www/projectlamp
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

The default Apache site was disabled and the project virtual host was enabled:

sudo a2dissite 000-default.conf
sudo a2ensite projectlamp.conf
sudo apache2ctl configtest
sudo systemctl reload apache2

I also updated Apache's directory index configuration so that `index.php` takes priority over `index.html`, allowing the PHP entry point to load by default.

## 6. Verifying PHP Through Apache

Finally, I created a PHP test script in the project's document root and verified that Apache correctly processed the PHP file.

echo "<?php phpinfo(); ?>" | sudo tee /var/www/projectlamp/index.php

The resulting PHP information page confirmed that PHP was being executed through Apache.

## Verification Matrix

| Component    | Verification                 | Expected Result           |
| ------------ | ---------------------------- | ------------------------- |
| Ubuntu       | SSH connection               | Server accessible         |
| Apache       | `systemctl status apache2`   | Service active            |
| Apache       | `apache2ctl configtest`      | Configuration valid       |
| HTTP         | `curl http://localhost:80`   | HTTP response returned    |
| MySQL        | Authenticated database login | Database accessible       |
| PHP          | `php -v`                     | PHP installed             |
| PHP + Apache | PHP test page                | PHP executed successfully |
| Virtual Host | Browser request              | Project served by Apache  |

## Security Considerations

- The EC2 private key used for SSH access is not committed to this repository.
- Credentials from the original setup are not included in the published documentation.
- MySQL was secured using `mysql_secure_installation`.
- Screenshots and documentation should be reviewed before publication to ensure that private IP addresses, instance IDs, usernames, local key paths, and other sensitive information are not exposed.

## Repository Structure

lampstaack-proj/
│
├── README.md
├── Lampproject.dox.docx
└── screenshots/
├── 01-ec2/
├── 02-apache/
├── 03-mysql/
├── 04-php/
├── 05-virtual-host/
└── 06-verification/

The original detailed documentation contains the full step-by-step deployment process from which this README was created. The documentation can later be migrated into native Markdown under `docs/` for easier searching and navigation on GitHub.

## Tech Stack & Skills Demonstrated

AWS EC2 · Ubuntu Linux · Apache · PHP · MySQL · SSH · HTTP · Bash/Linux CLI

- Cloud infrastructure provisioning and SSH-based server administration
- Apache installation, virtual host configuration, and service management
- MySQL installation, security hardening, and authenticated access
- PHP installation and integration with Apache and MySQL
- Ubuntu package management using `apt`
- Apache configuration validation and troubleshooting
- HTTP testing using `curl`
- End-to-end deployment verification

## Project Outcome

A functional LAMP environment was deployed on AWS EC2, with Apache, PHP, and MySQL configured and integrated. The environment was verified through local testing and external access through the Apache virtual host.
