# WordPress Security Implementation and Vulnerability Management on Ubuntu VM

## Overview
This document provides a comprehensive guide on installing WordPress on an Ubuntu Server VM, conducting a vulnerability scan, auditing the identified vulnerabilities, explaining each vulnerability in simple terms, demonstrating potential exploits, and securing the vulnerabilities.

## Prerequisites
1. Ubuntu Server VM from the previously created infrastructure.
2. Internet access on the Ubuntu Server VM.
3. Administrative access to the Ubuntu Server VM.

## Step-by-Step Guide

### 1. Install WordPress on Ubuntu VM

#### 1.1 Update and Upgrade the System
Ensure your system is up-to-date:
```sh
sudo apt update
sudo apt upgrade -y
```

#### 1.2 Install Apache Web Server
Install and start Apache:
```sh
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
```

#### 1.3 Install MySQL Database Server
Install MySQL and secure the installation:
```sh
sudo apt install mysql-server -y
sudo mysql_secure_installation
```
Follow the prompts to configure security options for MySQL.

#### 1.4 Install PHP
Install PHP and necessary modules:
```sh
sudo apt install php libapache2-mod-php php-mysql -y
```

#### 1.5 Download and Install WordPress
Download WordPress and set up the directory:
```sh
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar -xvf latest.tar.gz
sudo mv wordpress /var/www/html/
```

#### 1.6 Configure WordPress
Set appropriate permissions:
```sh
sudo chown -R www-data:www-data /var/www/html/wordpress
sudo chmod -R 755 /var/www/html/wordpress
sudo cp /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php
```

#### 1.7 Create MySQL Database and User for WordPress
Create the database and user:
```sh
sudo mysql -u root -p
```
In the MySQL shell, execute:
```sql
CREATE DATABASE wordpress_db;
CREATE USER 'wordpress_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

#### 1.8 Configure Apache for WordPress
Create and edit the WordPress site configuration:
```sh
sudo nano /etc/apache2/sites-available/wordpress.conf
```
Add the following configuration:
```apache
<VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/wordpress
    ServerName example.com
    ServerAlias www.example.com

    <Directory /var/www/html/wordpress/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable the site and rewrite module:
```sh
sudo a2ensite wordpress.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```

#### 1.9 Complete WordPress Installation via Web Browser
Open a web browser and navigate to `http://your_server_ip` to complete the WordPress installation through the web interface.

### 2. Scan for Vulnerabilities

#### 2.1 Install WPScan
Install Ruby and WPScan:
```sh
sudo apt install ruby-full
sudo gem install wpscan
wpscan --update
```

#### 2.2 Run WPScan
Scan your WordPress site:
```sh
wpscan --url http://your_server_ip/wordpress --enumerate vp
```
The scan will identify vulnerabilities in WordPress, plugins, and themes.

### 3. Audit and Explain Vulnerabilities

#### Common WordPress Vulnerabilities

1. **Outdated WordPress Version**
   - **Explanation**: Running an outdated WordPress version can expose the site to known vulnerabilities.
   - **Harm**: Hackers can use known exploits to gain unauthorized access or control of the site.
   - **Exploit Steps**:
     1. Identify the WordPress version using tools like WPScan.
     2. Search for known exploits for that version on databases like ExploitDB.
     3. Use the exploit code to target the vulnerable site.
   - **Solution**: Update WordPress to the latest version from the admin dashboard or via command line.
     ```sh
     cd /var/www/html/wordpress
     sudo wp core update
     ```

2. **Vulnerable Plugins/Themes**
   - **Explanation**: Plugins and themes add functionality but can also introduce security risks.
   - **Harm**: Exploits can range from data breaches to full site takeovers.
   - **Exploit Steps**:
     1. Identify installed plugins/themes and their versions using WPScan.
     2. Search for known vulnerabilities in those plugins/themes.
     3. Exploit the vulnerabilities using available PoC (Proof of Concept) scripts.
   - **Solution**: Regularly update all plugins and themes, and remove any that are not actively maintained or used.
     ```sh
     sudo wp plugin update --all
     sudo wp theme update --all
     ```

3. **Weak Passwords**
   - **Explanation**: Simple passwords are vulnerable to brute-force attacks.
   - **Harm**: Hackers can gain administrative access to the WordPress site.
   - **Exploit Steps**:
     1. Use a brute-force attack tool like Hydra or WPScan.
     2. Attempt to guess the admin password using a list of common passwords.
   - **Solution**: Use strong, complex passwords and enable two-factor authentication.
     1. Install a plugin like "WP Password Policy Manager" to enforce strong passwords.
     2. Enable two-factor authentication using plugins like "Google Authenticator" or "Two Factor Authentication".

4. **File Inclusion Vulnerabilities**
   - **Explanation**: Improper handling of file paths can allow attackers to include malicious files.
   - **Harm**: Can lead to remote code execution or data leakage.
   - **Exploit Steps**:
     1. Identify vulnerable file inclusion points using WPScan or manual testing.
     2. Craft a URL to include a remote file, e.g., `http://example.com/?file=../../../../etc/passwd`.
   - **Solution**: Validate and sanitize user inputs, and use secure coding practices.

5. **Cross-Site Scripting (XSS)**
   - **Explanation**: Injection of malicious scripts into web pages viewed by other users.
   - **Harm**: Can steal cookies, session tokens, or perform actions on behalf of users.
   - **Exploit Steps**:
     1. Identify input fields vulnerable to XSS using WPScan or manual testing.
     2. Inject a malicious script, e.g., `<script>alert('XSS')</script>`.
   - **Solution**: Sanitize and escape all user inputs, and use security plugins like "Wordfence".

6. **SQL Injection (SQLi)**
   - **Explanation**: Insertion of malicious SQL queries into input fields.
   - **Harm**: Can retrieve, modify, or delete database data.
   - **Exploit Steps**:
     1. Identify input fields vulnerable to SQLi using WPScan or manual testing.
     2. Inject a malicious SQL query, e.g., `http://example.com/?id=1 OR 1=1`.
   - **Solution**: Use prepared statements and parameterized queries, and validate user inputs.

7. **Cross-Site Request Forgery (CSRF)**
   - **Explanation**: Tricks users into executing unwanted actions on a web application.
   - **Harm**: Can perform actions on behalf of authenticated users without their knowledge.
   - **Exploit Steps**:
     1. Craft a malicious link or form that performs an action on the target site.
     2. Trick the user into clicking the link or submitting the form.
   - **Solution**: Implement CSRF tokens in forms and use security plugins like "All In One WP Security & Firewall".

8. **Directory Listing**
   - **Explanation**: Exposing directory contents can reveal sensitive information.
   - **Harm**: Attackers can discover sensitive files and directories.
   - **Exploit Steps**:
     1. Access a directory URL, e.g., `http://example.com/wp-content/uploads/`.
     2. View the list of files and directories.
   - **Solution**: Disable directory listing in Apache configuration.
     ```sh
     sudo nano /etc/apache2/apache2.conf
     ```
     Add the line:
     ```apache
     Options -Indexes
     ```
     Restart Apache:
     ```sh
     sudo systemctl restart apache2
     ```

### 4. Securing Vulnerabilities

#### 4.1 Update WordPress
Update WordPress to the latest version:
```sh
cd /var/www/html/wordpress
sudo wp core update
```

#### 4.2 Update Plugins and Themes
Update all plugins and themes:
```sh
sudo wp plugin update --all
sudo wp theme update --all
```

#### 4.3 Enforce Strong Passwords
Install a plugin like "WP Password Policy Manager" to enforce strong passwords and enable two-factor authentication using plugins like "Google Authenticator" or "Two Factor Authentication".

#### 4.4 Harden WordPress Configuration
Edit the `wp-config.php` file:
```sh
sudo nano /var/www/html/wordpress/wp-config.php
```
Add the following

 lines for security:
```php
define('DISALLOW_FILE_EDIT', true);
define('FORCE_SSL_ADMIN', true);
```

#### 4.5 Secure Apache Configuration
1. Disable directory browsing:
   ```sh
   sudo nano /etc/apache2/apache2.conf
   ```
   Add the line:
   ```apache
   Options -Indexes
   ```
   Restart Apache:
   ```sh
   sudo systemctl restart apache2
   ```

2. Install and configure a firewall:
   ```sh
   sudo apt install ufw
   sudo ufw allow 'Apache Full'
   sudo ufw enable
   ```

#### 4.6 Regular Backups
Set up regular backups using plugins like "UpdraftPlus" or configure automated scripts with cron jobs.

### Conclusion
This documentation provides a detailed guide to installing, securing, and auditing a WordPress installation on an Ubuntu Server VM. By following the steps outlined, you can ensure that your WordPress site is secure and resilient against common vulnerabilities. This documentation demonstrates a thorough understanding of the concepts learned in cybersecurity courses, including vulnerability management, practical exploitation, and securing web applications.
