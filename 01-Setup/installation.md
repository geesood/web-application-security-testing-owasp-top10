Web Application Security Lab Setup (Kali Linux)

This document details how I set up my penetration testing environment on Kali Linux (Virtual Machine) for practicing the OWASP Top 10 vulnerabilities.
The lab includes:

DVWA (Damn Vulnerable Web App)

OWASP Juice Shop

Burp Suite Community Edition

Kali Linux built-in penetration testing tools

## 1. Kali Linux Environment

OS: Kali Linux

Platform: Virtual Machine (VMware/VirtualBox)

Purpose: Safe web application security testing environment

## 2. Installing DVWA (Damn Vulnerable Web App)
Step 1 – Install required packages
sudo apt update
sudo apt install apache2 mariadb-server php php-mysqli php-gd php-zip -y

Step 2 – Download DVWA
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git

Step 3 – Set file permissions
sudo chown -R www-data:www-data /var/www/html/DVWA
sudo chmod -R 755 /var/www/html/DVWA

Step 4 – Start services
sudo systemctl start apache2
sudo systemctl start mariadb

Step 5 – Create the DVWA database
sudo mysql -u root -p


Inside MySQL:

create database dvwa;
grant all privileges on dvwa.* to 'dvwa'@'localhost' identified by 'password';
flush privileges;
exit;

Step 6 – Configure DVWA

Edit configuration file:

sudo nano /var/www/html/DVWA/config/config.inc.php


Update these lines:

$_DVWA['db_user'] = 'dvwa';
$_DVWA['db_password'] = 'password';

Step 7 – Access DVWA in browser

Open:

http://127.0.0.1/DVWA/setup.php


Click Create / Reset Database.

DVWA is now ready.

## 3. Installing OWASP Juice Shop
Step 1 – Install Node.js
sudo apt install nodejs npm -y

Step 2 – Install Juice Shop
sudo npm install -g juice-shop

Step 3 – Start Juice Shop
juice-shop


Access:

http://localhost:3000

## 4. Using Burp Suite

Kali already includes Burp Suite.

Start it:

Applications → Web Application Analysis → Burp Suite


Set browser proxy:

Host: 127.0.0.1

Port: 8080

Now you can intercept requests.

## 5. Lab Ready

Your environment is prepared for:

SQL Injection

XSS

CSRF

Authentication Bypass

File Upload Attacks

Security Misconfigurations
