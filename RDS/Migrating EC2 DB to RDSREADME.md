# 🚀 AWS Lab: Migrating a WordPress Database from EC2 to Fully Managed RDS

## 📝 Overview
In this hands-on lab, we will migrate an existing WordPress database hosted on a self-managed MariaDB EC2 instance to a fully managed **Amazon Relational Database Service (RDS)** MySQL instance. This migration enhances our infrastructure by leveraging AWS's managed database features like automated backups, high availability, and scalability.

---

## 🏗️ Current Architecture (Before Migration)
* **WordPress Server:** Running on an Amazon EC2 instance.
* **Database Server:** MariaDB running on a separate Amazon EC2 instance.

<img width="773" height="188" alt="image 1" src="https://github.com/user-attachments/assets/c36c041e-d6d0-4dd8-b6c1-652aecca696d" />

* **Current State Validation:** We have a test post on our WordPress site currently stored in the self-managed MariaDB EC2 instance.

<img width="958" height="387" alt="image 2" src="https://github.com/user-attachments/assets/3b90c199-e4e3-49d0-a266-90f96d4c8569" />

---

## 🛠️ Step 1: Create a DB Subnet Group
Before provisioning an RDS instance, we must define exactly where it will reside within our Virtual Private Cloud (VPC) across multiple Availability Zones.

1. Navigate to the **RDS Console** and select **Subnet groups** from the left-hand menu.
<img width="958" height="413" alt="image 3" src="https://github.com/user-attachments/assets/803087aa-3065-4350-94a7-f41529c2aa43" />

2. Click **Create DB subnet group**. Provide a Name, Description, and select the pre-created VPC (`a4l-vpc1`).
<img width="923" height="342" alt="image 4" src="https://github.com/user-attachments/assets/960a99d1-5fef-4684-89d8-59db9cdf892c" />

3. Add the necessary subnets distributed across different Availability Zones to ensure high availability.
<img width="959" height="413" alt="image 5" src="https://github.com/user-attachments/assets/075b03aa-19aa-4566-9df6-63d9b56c9af7" />
<img width="943" height="279" alt="image 6" src="https://github.com/user-attachments/assets/1ab66671-7c43-4c70-8ec1-219fb4d2abf7" />

4. Confirm the successful creation of the Subnet Group.

<img width="959" height="335" alt="image 7" src="https://github.com/user-attachments/assets/d302e0c4-446c-4d77-b9a7-c70f7f10ac32" />

---

## ☁️ Step 2: Provision the AWS RDS Database
Now, we deploy the fully managed database instance.

1. Go to **Databases** and click **Create database**. Select the **MySQL** engine.
<img width="938" height="352" alt="image 8" src="https://github.com/user-attachments/assets/f3c83e94-c5d0-4b4b-9a88-cfd9053b1593" />

2. Choose the deployment template (e.g., Sandbox for testing) and configure instance options.
<img width="943" height="355" alt="image 9" src="https://github.com/user-attachments/assets/c58d4e42-7dba-4615-b038-23b74b875249" />

3. Under **Settings**, set the `DB instance identifier` and configure the **Master username** and password.
<img width="947" height="355" alt="image 10" src="https://github.com/user-attachments/assets/4ec270b2-88a7-412b-8afb-f125d81143e7" />

4. In the **Connectivity** section, ensure **Public access** is set to **No** (for security) and create a **New VPC security group** (`a4lvpc-rds-sg`).
<img width="944" height="319" alt="image 11" src="https://github.com/user-attachments/assets/106fec61-b3db-4bbc-bc8a-8ce82035654a" />

5. Click **Create database** and wait for the status to change from *Creating* to *Available*.

<img width="959" height="410" alt="image 12" src="https://github.com/user-attachments/assets/76da2421-f202-4427-9596-ce4e883065bd" />

---

## 🔒 Step 3: Configure Security Groups
To allow our WordPress EC2 instance to communicate with the new RDS instance, we must update the firewall rules.

1. Navigate to the **EC2 Console** -> **Security Groups**.
2. Select the newly created RDS Security Group (`a4lvpc-rds-sg`) and edit the **Inbound rules**.
3. Add a rule for **MySQL/Aurora (Port 3306)** and set the source to the Security Group ID of the WordPress EC2 instance. Save the rules.

<img width="948" height="355" alt="image 13" src="https://github.com/user-attachments/assets/bea29ab9-e253-4243-a81b-4198dae85e01" />

---

## 📦 Step 4: Backup the Source Database
We need to extract the existing data from our self-managed MariaDB EC2 instance.

1. SSH into the **WordPress EC2 instance**.
2. Run the `mysqldump` command pointing to the *Private IP* of the MariaDB instance to create a SQL backup file:

bash
# Syntax: mysqldump -h <PRIVATE_IP_OF_MARIADB> -u <USER> -p <DATABASE_NAME> > <FILE_NAME.sql>
mysqldump -h 10.16.51.117 -u a4lwordpress -p a4lwordpress > a4lwordpress.sql

Verify the file was created using ls -la.

<img width="872" height="172" alt="image 14" src="https://github.com/user-attachments/assets/fe4baaad-410f-4650-8e90-ac98f442737e" />

⚠️ Troubleshooting & Lessons Learned: The Missing Database
🔴 The Problem:
When immediately attempting to import the a4lwordpress.sql file into the new RDS instance, we encountered the following error:

ERROR 1049 (42000): Unknown database 'a4lwordpress'

🔍 Why Did This Happen?
During the creation of the RDS instance (Step 2), we left the "Initial database name" field blank under the Additional configurations section. As a result, AWS provisioned the database server (the physical engine) but did not create the logical database schema (the container for our tables) inside it. The import command failed because it was trying to inject data into a database that did not exist.

✅ The Solution:
We must manually create the logical database inside the RDS instance before importing data.

Connect to the RDS instance using the master credentials:

Bash
mysql -h a4lwordpress.cgt4eueme91a.us-east-1.rds.amazonaws.com -u a4lwordpress -p
Once connected to the MySQL prompt, create the database manually:

SQL
CREATE DATABASE a4lwordpress;
Exit the MySQL prompt:

SQL
exit;
The destination is now prepped and ready for data!

🔄 Step 5: Restore Data to the RDS Instance
With the logical database created, we can now push our SQL dump into the RDS instance.

Locate the RDS Endpoint in the RDS Console under the Connectivity & security tab.

<img width="950" height="356" alt="image 15" src="https://github.com/user-attachments/assets/fbf0d603-f1b2-4c07-8d3d-694dbb13c862" />

Run the import command from the WordPress EC2 instance:

Bash
# Syntax: mysql -h <RDS_ENDPOINT> -u <USER> -p <DATABASE_NAME> < <FILE_NAME.sql>
mysql -h a4lwordpress.cgt4eueme91a.us-east-1.rds.amazonaws.com -u a4lwordpress -p a4lwordpress < a4lwordpress.sql
(No output means the command executed successfully!)

⚙️ Step 6: Update WordPress Configuration
The final step is to redirect the WordPress application to use the new RDS database instead of the old MariaDB instance.

Open the WordPress configuration file on the EC2 instance:

Bash
cd /var/www/html
sudo nano wp-config.php
Locate the DB_HOST line and replace the old MariaDB Private IP with your new RDS Endpoint.

PHP
/** MySQL hostname */
define( 'DB_HOST', 'a4lwordpress.cgt4eueme91a.us-east-1.rds.amazonaws.com' );

<img width="963" height="409" alt="image 16" src="https://github.com/user-attachments/assets/bda41a99-9cc6-46c6-b7f0-473abf6d436f" />

Save the file and exit the editor.

🎉 Verification
Refresh your WordPress website in the browser. The site should load perfectly, displaying your existing posts, confirming that the application is now successfully reading from and writing to the AWS RDS instance!

<img width="935" height="251" alt="image 17" src="https://github.com/user-attachments/assets/93f9df3a-4372-4721-bb56-7a7a2f2c3193" />

Lab Complete! ✅
