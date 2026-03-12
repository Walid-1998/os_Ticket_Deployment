<p align="center">
<img width="600" alt="osTicket Logo" src="images/osticket logo.png" />

</p>

<h1>osTicket Help Desk Lab [Ubuntu / VirtualBox]</h1>

This lab demonstrates how to deploy and configure a helpdesk ticketing system using osTicket in a virtualized environment.

The purpose of this project is to simulate a real-world IT support environment where users submit tickets and helpdesk agents resolve issues.

<h2>Environments and Technologies Used</h2>
<p>
  <img src="https://skillicons.dev/icons?i=ubuntu,mysql,php" />
  <img width="45" alt="osticket icon" src="images/osticket icon.png" />
  <img width="45" alt="virtualbox icon" src="images/virtualbox.png" />
</p>

- Oracle VirtualBox (Virtual Machines)
- osTicket (Ticket System)
- Mysql
- PHP

<h2>Operating Systems Used </h2>

- Linux (Ubuntu)

<h2>High-Level Deployment and Configuration Steps</h2>

> [!IMPORTANT]
> Each step includes written instructions followed by a screenshot.
Expand the See screenshots section to view the images.

> [!IMPORTANT]
> Make Sure You Have Enable Copy and Paste in Oracle VirtualBox

Settings > General > Advanced > Set Both Options To Bidirectional 
<details><summary>See screenshots</summary>
<img src="images/copy paste vm 1.jpg" width="60%" >
<img src="images/copy paste vm 2.jpg" width="60%" >
<img src="images/copy paste vm 3.jpg" width="60%" >
</details>

<h3>Step 1 — Downloading Ubuntu Operating System & Oracle VirtualBox</h3>

[Oracle VirtualBox](https://www.virtualbox.org)
<details><summary>See screenshots</summary>
<img src="images/image of vm download.png" width="60%" >
</details>

[Ubuntu](https://ubuntu.com/download/desktop)
<details><summary>See screenshots</summary>
<img src="images/image of ubuntu download.png" width="60%" >
</details>

<h3>Step 2 — Setting Up The Virtual Machine</h3>
Lets setup our virtual machine:

* In this step we connect to the ubuntu virtual machine using VirtualBox.  
This allows us to access the server environment where the osTicket system and its required components will be installed and configured.

<details><summary>See screenshots</summary>
<img src="images/oracle virtualbox.png" width="60%" >
<img src="images/installing the vm.png" width="60%" >
<img src="images/os install.png" width="60%" >
<img src="images/vm hardware.png" width="60%" >
<img src="images/samenvatting.png" width="60%" >
</details> 

<h3>Step 3 — Setting Up osTicket in your ubuntu vm</h3>
Login & Update | After Ubuntu Is Installed

```
sudo apt update && sudo apt upgrade -y
```

<h3>Step 4 — Install LAMP Stack</h3>
osTicket needs:

Apache

MySQL / MariaDB

PHP

```
sudo apt install apache2 -y
```

```
sudo systemctl enable apache2
sudo systemctl start apache2
```

```
http://localhost
```

<h3>Step 5 — Install MariaDB</h3>

```
sudo apt install mariadb-server -y
```

Secure it:

```
sudo mysql_secure_installation
```

Say:

Remove anonymous users → Yes

Disallow root remote login → Yes

Remove test DB → Yes

<h3>Step 6 Install PHP</h3>

```
sudo apt install php php-mysql php-imap php-apcu php-intl php-gd php-mbstring php-xml php-cli php-curl unzip -y
```

```
sudo systemctl restart apache2
```

<h3>Step 7 — Database Setup</h3>

Login:
```
sudo mysql -u root -p
```

Create DB:
```
CREATE DATABASE osticket;
CREATE USER 'osticketuser'@'localhost' IDENTIFIED BY 'StrongPassword';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticketuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
<h3>Step 8 — Install osTicket</h3>

Download latest:
```
cd /tmp
wget https://github.com/osTicket/osTicket/releases/download/v1.18.1/osTicket-v1.18.1.zip
unzip osTicket-v1.18.1.zip
```

Move to web root:
```
sudo mv upload /var/www/html/osticket
```

Set permissions:
```
sudo chown -R www-data:www-data /var/www/html/osticket
sudo chmod -R 755 /var/www/html/osticket
```
<h3>Step 9 - Configure Apache</h3>

Create config:
```
sudo nano /etc/apache2/sites-available/osticket.conf
```
```
<VirtualHost *:80>
    ServerAdmin admin@localhost
    DocumentRoot /var/www/html/osticket
    ServerName osticket.local

    <Directory /var/www/html/osticket>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/osticket_error.log
    CustomLog ${APACHE_LOG_DIR}/osticket_access.log combined
</VirtualHost>
```

Enable:
```
sudo a2ensite osticket.conf
sudo a2enmod rewrite
sudo systemctl restart apache2
```
Final File Permission:
```
sudo cp /var/www/html/osticket/include/ost-sampleconfig.php /var/www/html/osticket/include/ost-config.php
sudo chmod 666 /var/www/html/osticket/include/ost-config.php
```
<h3>Step 10 — Web Installer</h3>

Open browser in Ubuntu VM:
```
http://localhost/osticket/setup
```
Fill:

Field	Value
Helpdesk Name	Your Company
Email	admin@email.com

DB Name	osticket
DB User	osticketuser
DB Pass	StrongPassword

Click Install Now

After Install (IMPORTANT):

Lock config:
```
sudo chmod 644 /var/www/html/osticket/include/ost-config.php
```
Delete setup folder:
```
sudo rm -rf /var/www/html/osticket/setup/
```

## 🎫 Ticket Workflow Demonstration

After installing osTicket, the system was tested by simulating a real support request.

The workflow demonstrates how a user submits a ticket and how an administrator manages and resolves it through the osTicket dashboard.

### User Submits a Ticket

A user accesses the helpdesk portal and submits a support request.

<details><summary>See screenshots</summary>
<img src="images/support center.png" width="60%" >
<img src="images/os ticket open a new ticket 2.png" width="60%" >
</details>

The user fills out the ticket form including:

- Name
- Email address
- Help topic
- Description of the issue

Once submitted, the ticket is stored in the osTicket database and becomes visible to helpdesk agents.

### Admin Dashboard

The ticket appears in the osTicket agent dashboard where support staff can review and manage incoming requests.

<details><summary>See screenshots</summary>
<img src="images/os ticket john tickets.png" width="60%" >
<img src="images/os ticket john ticket from karen.png" width="60%" >
</details>

Support agents can:

- View ticket details
- Assign tickets to staff members
- Respond to the user
- Update ticket status

### Configure Roles
Admin Panel -> Agents -> Roles
<details><summary>See screenshots</summary>
<img src="images/admin panel roles.png" width="60%" >
<img src="images/admin panel create new role.png" width="60%" >
</details>

### Configure Departments
Admin Panel -> Agents -> Departments
<details><summary>See screenshots</summary>
<img src="images/department 1.png" width="60%" >
<img src="images/admin panel departments 2.png" width="60%" >
</details>

### Configure Teams
Admin Panel -> Agents -> Teams (Pull Agents from different Departments)
<details><summary>See screenshots</summary>
<img src="images/teams.png" width="60%" >
<img src="images/admin panel teams.png" width="60%" >
</details>

Allow anyone to create tickets
Admin Panel -> Settings -> User Settings (UNCHECK: unregistered users can create tickets)
Registration Required: Require registration and login to create tickets

### Configure Agents (workers)
Admin Panel -> Agents -> Add New
- Jane (Dept: SysAdmins)
- John (Dept: Support)

### Configure Users (customers)
Agent Panel -> Users -> Add New
- Karen
- Ken

### Configure SLA
Admin Panel -> Manage -> SLA
- Sev-A (Grace Period: 1 hour, Schedule: 24/7)
- Sev-B (Grace Period: 4 hours, Schedule: 24/7)
- Sev-C (Grace Period: 8 hours, Business Hours)
<details><summary>See screenshots</summary>
<img src="images/admin panel sla.png" width="60%" >
<img src="images/admin panel add SLA.png" width="60%" >
<img src="images/Admin panel Sla Sev-A.png" width="60%" >
<img src="images/admin panel sla sev b.png" width="60%" >
<img src="images/admin panel sla sev c.png" width="60%" >
</details>

### Configure Help Topics (For when users create a ticket)
Admin Panel -> Manage -> Help Topics
- Business Critical Outage
- Personal Computer Issues
- Equipment Request
- Password Reset
- Other
<details><summary>See screenshots</summary>
<img src="images/admin panel add help topics.png" width="60%" >
<img src="images/admin panel help topics done.png" width="60%" >
</details>
