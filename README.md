# Azure data base migration
The aim of this project was to architect and implement a cloud-based database system on [Microsoft Azure](https://azure.microsoft.com/en-gb), to showcase hands-on expertise in cloud engineering.
This begins with establishing a production environment database and then migrating the database to Azure SQL Database, focusing on crucial aspects like data backup, restoration, and automated scheduling.
After this, I will simulate a disaster recovery scenario with potential data loss. In addition to this, using geo-replication and failover configuration, I will ensure data availabilty even under challenging conditions.
Finally, to enchance security, Microsoft Entra ID intergration will be employed to define access roles, adding an extra layer of control and protection.


# Production Environment set up

## Provisioning a Windows Virtual Machine

The first step of this project is establishing the foundation of the cloud-based database sytem on Azure. To do this, I set up a Windows Virtual Machine (VM) to serve as the cornerstone of the production environment. This VM will emulate the functions of a Windows server, replicating the operations of an on-premise system within a company. Throughout the project this VM will act as a repository for the company's database. This set up effectively simulates a secure and dedicated data storage solution.

Working in the Azure portal, I first created a resource group to store the resources which would be used throughout this project. Then I used the 'Create Resource' option and chose a Virtual Machine. For the set up of this, I chose a relevant name relating to the use of this VM, as well as selecting the region it would be hosted in. It is best practice to select the region which is geographically closest to your actual geographical location, to minimise latency. I also chose an Image, which represents the operating system of the VM. I selected Windows 10 Pro, as this was the operating system I felt most comfortable with. 

Next I selected the size of the VM, which refers to the size of memory and CPU. I used Standard B2ms. Finally, I had to specify the username and password which would be used when connecting to the VM, as well as amending the inbound port rules, in order to enable access for Remote Desktop Protocol (RDP). This would allow me to connect to the VM using my local machine, also running Windows 10.

Once I had completed the set up process and deployed the VM, I was now able to connect to it. In order to do this, I downloaded the RDP file from the VM's connection page. I was then able to use this on my local machine to access and login to the VM, using the previously mentioned credentials.

## Installing SQL Server and Microsoft SQL Server Management Studio (SSMS)

Following the successful deployment of the VM and then connecting to it, I then needed to install [SQL server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) and [SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16) on the VM. These tools are essential for proficient database management within the production environment, mirroring the capabilities of a corporate database server.

SQL Server is a robust and widely used relational database management system (RDBMS) developed by Microsoft. It is designed to store and manage vast amounts of data while providing efficient and secure data processing capabilities. SQL Server supports both structured query language (SQL) and Transact-SQL (T-SQL) for querying and manipulating data.

SQL Server Management Studio (SSMS) is a powerful graphical user interface (GUI) tool provided by Microsoft for managing SQL Server instances and databases. It serves as a central hub for database administrators and developers to perform various tasks related to SQL Server.

After installation, I connected to SQL Server using SSMS, as this provides a user-friendly interface for interacting with databases and performing various administrative tasks.

## Creating the Production Database

After the installation of SQL Server and SSMS, I was now able to create the production database by restoring it from a provided backup file. The backup file corresponds to the renowned Microsoft SQL Server database known as AdventureWorks. This database is an illustrative and comprehensive sample database that emulates a fictional manufacturing company's operations. It encompasses various tables, views, stored procedures, and data, offering a rich and diverse dataset. By restoring the AdventureWorks database from the provided backup file, I was able to replicate an authentic production database scenario.
To do this, after downloading the AdventureWorks `.bak` file, I moved it to the SQL Server folder in the VM. As I had already connected to SQL Server in SSMS, I was then able to initiate Database Restore using the AdventureWorks backup file.

# Migration to Azure SQL database

Now that the on-premise database has been set up, I can now focus on transitioning the database to Azure's cloud ecosystem, an Azure SQL Database.

## Set up of Azure SQl database

Azure SQL Database is a fully-managed, Platform-as-a-Service (PaaS) offering within Azure, built on the foundation of Azure SQL Server. Azure SQL Server is a cloud-based implementation of Microsoft SQL Server, a widely-used relational database management system (RDBMS). It provides a fully-managed environment for hosting SQL databases in the cloud, eliminating the need for on-premises hardware and maintenance.
Azure SQL Database shares the same core capabilities as Azure SQL Server but adds additional benefits by abstracting away most of the infrastructure management tasks.

In order to set up an Azure SQL Database, I simply navigated to the SQL Databases page in the Azure portal, and selected 'Create' in order to set up a new database. After choosing an appropriate name for the database, I then had to create a SQL Database Server, which is a logical container for databases, and provides a centralized location to manage database resources. The server required me to choose a name and region, which again, should be whichever is geographically closest to my location. I also had to choose an Authentication Method for this server, I used SQL Server Authentication.

After creating the server, I was now able to finish creation of the Azure SQL Database. In the Configure Database page, I selected the service tier for the database. Azure SQL Database offers different tiers of service for different workloads and performance requirements. One of the options for storage tier is Basic Compute, which provides a low-cost option for small databases or those with modest performance needs. Basic Compute is a good option for test and development environments, small-scale applications, and low-traffic websites. I chose Basic in this case, which allowed me to begin deployment of the database.

Finally for this database, I needed to change the setting of the database. By default, Azure SQL Database is configured to deny all public connections. To connect to a database from outside of Azure, public access will need to be enabled by configuring a firewall rule that allows traffic from the client IP address. Public network access to a SQL Server instance is controlled by the Public Network Access setting. This setting determines whether traffic from the public internet is allowed to reach your SQL Server instance. I changed the Public Network Access setting to 'Selected Networks' and added the IP address of the production VM as a firewall rule.

## Preparing for Migration

In order to complete a database migration [Azure data studio](https://azure.microsoft.com/en-gb/products/data-studio) must be downloaded and installed on the production VM. Azure Data Studio provides a feature-rich environment, catering to the diverse needs of database administrators, developers, and data professionals. Azure Data Studio empowers users to manage various data platforms, including SQL Server, Azure SQL Database, PostgreSQL, MySQL, and more. With its seamless integration with Azure services, it offers a unified interface to manage both local and cloud-based databases, making it the perfect companion for database migration.

After installing Azure Data Studio, I was able to connect to the previously set up local database, which contains information from the AdventureWorks backup. I was now able to explore the contents of the AdventureWorks database using Azure Data Studio.

## Connecting to Azure SQL Database

Having connected to the local database in Azure Data Studio, I was now able to connect to the Azure SQL Database using the SQL login credentials used when setting up the SQL Server.

## Schema migration 

Now that connection has been established to both the local database, and the Azure database, I installed the [SQL Server Schema Compare extension](https://learn.microsoft.com/en-us/azure-data-studio/extensions/schema-compare-extension) in Azure Data Studio. This powerful extension streamlines the migration process, enabling you to compare and apply schema changes between local databases and the SQL Database in Azure Data Studio.

First, I start with migrating the schema of the local database to the Azure-hosted database. Using the extension, I selected the source database, the locally saved database, and the Azure-hosted Database. I was then able to apply the schema from the local database to the Azure-hosted database so that the structure of the local database, including tables, views, stored procedures, and other objects, will now match that of the Azure SQL Database. The schema migration sets the foundation for further steps in the database migration process.

## Data Migration

Now that the schema have been successfully migrated, I was now able to migrate the data. In order to do this I installed the [Azure SQL Migration extension](https://learn.microsoft.com/en-us/azure-data-studio/extensions/azure-sql-migration-extension), a powerful tool designed to streamline and simplify the migration of data between these environments. Using this extension, i was able to migrate the data. 

In order to use this extension, I first had to create an Azure Database Migration Service, which will orchestrate the database migration. This was done in the Azure portal, where I selected the Source server type, SQL Server, and the Target server type, Azure SQL Database. This was created within the same resource group set up for this project.

Having set up the migration service, I was able to begin the migration in Azure Data Studio. Using the Azure SQL Migration Extension, I entered and confirmed the details of the source and target databases. I also needed to register the migration service, by setting up integration runtime to handle connectivity between source and target. After registering this, and providing authentication for the SQL server, the extension then compares the source and target databases, to check that a valid migration can be performed. Then I was able to begin the data migration.

## Validate migration

I was then able to confirm that the data had been successfully migrated, by comparing the information within the tables, to ensure there was no loss of rows or columns within the tables

# Database backup and restore.

In this part of the project, I focused on ensuring the data stored in the production database is securely stored on Azure, before creating a development environment for the database.

Data loss or corruption can have severe consequences for any organization, leading to financial losses, reputation damage, and potential legal liabilities. In the Azure cloud environment, Azure SQL Database serves as a critical repository for valuable data powering applications and business operations. However, with the rise of cyber threats and unexpected events, ensuring data protection is more crucial than ever.

Companies often have two types of database: one for storing real customer data (production database) and another for testing and experimenting (development database). By provisioning a development database, I am able to test and experiment without worrying about affecting the main production data. This seperation is crucial for making changes and improvements while maintaining the integrity of the live data.

I also created an automated backup solution for the development environment. This solution safeguards ongoing work, aids developers in efficiently testing changes, and enables swift recovery from errors or data loss.

## Back up on-premise database

A backup is essentially a copy of a database at a specific point in time, preserving its state and critical information. 

I created a backup file of the local database which would be saved on the VM. This was done by selecting the AdventureWorks database stored on the VM and creating a full restore backup file (`.bak`) in SSMS.

## Upload Backup to Blob storage

After I had created the backup file, I then created a storage account and container in Azure for this project within the resource group for this project, in order to take advantage of [Blob Storage](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blobs-overview) in Azure. This will serve as a secure online repository for the database backup. Then I was able to upload the backup file to this container through the Azure portal. This step provides an extra layer of backup protection through the presence of a redundant copy stored remotely.


## Restoring the database to a development environment

A development environment is a controlled and isolated space where software developers and testers can work on applications, test new features, and identify and fix bugs without affecting the production environment. 

In order to do this, I would deploy a new VM, this one to be used as a development environment. I began by following the same steps used to set up the original VM, [here](##Provisioning-a-Windows-Virtual-Machine), including installing SQL Server and SSMS again, as done [above](##-Installing-SQL-Server-and-Microsoft-SQL-Server-Management-Studio-(SSMS))

Once the development VM was created, I was then able to use the previously saved backup file, to reproduce the database on the development VM. I did this by downloading the AdventureWorks `.bak` file from the Azure storage container to the development VM, then used the same steps as [before](##-Creating-the-Production-Database) in order to restore from the backup file.

## Automating Backups for the Development Database

The next step was to utilise SSMS to establish a Management Task that automates regular backups of the development database. First, I had to create a SQL Server Credential, a security object that allows SQL Server to access external resources securely. It is particularly useful when you need to interact with external systems, such as Azure Blob Storage, to store or retrieve data. This was done using the access keys for the storage container in which the backups would be stored.

After the SQL Server Credential was set up, I was then able to connect to the SQL Server Agent node within the database. Using the credentials, I was able to create a maintenance plan, which would create a full backup file of the database and upload it to the Azure storage container. After confirming that it was working correctly by checking if the backup had been correctly uploaded, I then amended the plan to occur weekly. This would ensure consistent protection for any evolving wokr and simplify recovery for the development environment if needed.

# Disaster recovery simulation
Disaster recovery is a critical aspect of any organization's data strategy. It refers to the set of processes and tools implemented to safeguard data, applications, and systems, enabling rapid recovery and continuity of operations in the face of disruptive events. By having a robust disaster recovery plan, businesses can minimize downtime, ensure data integrity, and maintain their ability to serve customers without significant interruptions.

## Mimicking data loss in production environment

By intentionally mimicking data loss in the production environment, this will give me firsthand experience in recovery procedures. 

In order to mimic data loss on the Azure SQL Database within Azure Data Studio, I ran an SQL query which would remove a table from the database. I then confirmed that the table had been removed to simulate the loss of data.

## Restoring the data using Azure SQL Database Backup

In order to recover the intentionally lost data, I would use Azure's SQL Database Backup feature. Within the Azure portal, I navigated to the SQL Database dashboard, selected the target database, then selected the restore option. I was then able to select the Restore Point, which represents the point in time before the data loss occured. I chose a time before I had removed the table from the database. I then chose a name for the restored database.

After this new database had been deployed, I connected to it through Azure Data Studio. At this point I was able to confirm that the database had been successfully restored, as the previously deleted table was included in the restored database. This restored database would now act as the production database, and the previously altered database was deleted to avoid confusion in the future.

# Geo replication
Geo Replication with Azure SQL databases is a powerful disaster recovery feature. It works by simple replicating the determined data base in a seperate geographical location. Protecting from data loss in the primary geographical location.

## Set up Geo-replication for Azure SQL database.
Acheiving this with Azure services is fairly simple. I achieved it for this databse migration after genearting the restored SQL database. Selecting the the restored SQL database from the Azure portal. I selected the Replicas options under the data managment section. This takes you to another GUI with the options to generate a new SQL Server where I specified the name and location of the new server for Geo Replication  to a different *Region* to the Initial Primary server must be selected. In my case I chose East US. 

Again SQL Authetication has to set up.Then I selected review + create and then create to generate the replication. Using the resource page successful replication can be checked by reviewing if the replication has an associated primary database.

## Failover/tailback Testing
When generating a geo replicate it is important to test the system using a failover (awtiching the workload from primary region to the secondary region) and then a tailback (the reversion from the workload back to primary region after succesful failover)


### Test Failover/tailack
To do this I used the Azure SQL server from Azure portal and selcted the Failover gropus from the data manamgemnt options and add group funcitonality in failover groups page. The failover group was then named and and secondary server (Replica sever generated above) Set in the East Us choosen and finally generated by using Create. From the Portal I navigated to the replica server and access the failover group page and then select Failover options. If succesufl the primary and secondary databse labels should be switched. The tailback was schieved by simple selecting failover option again and noting the switch in the databases primary and secondary status.

# Microsoft Entra directory integration
The following step decription is about using the availible tooling of Microsoft Azure to increase the security around the databases we have migrated on the Azure SQL database services. Using Microsoft Entra ID.

## Create Azure SQL Microsoft entra Admin account
Using the Microsoft Azure portal I navigated to my replica SQL server, whilst first noting we were in the correct geo-region (South UK). Under the security options I selected Microsoft entra id. This sennt me to portal page that allowed for set admin functionality. Using this option going from Users and selecting myself among them. Setting me as the server admin. From this I tested the Entra credentials by using Azure data studio to connect to the server.

## Create DB Reader user

The final step of the project was to generate DB reader user. To do this I navigated to the Azure portal and navigate to the Microsoft Entra ID homepage and created a new user (with a suggestive name). The new user must be given a password. With a connection to SQl database from [step above](#create-azure-sql-microsoft-entra-admin-account) using microsfot entra credentials. Then using SQL Query to grant the db_datareader user role.

The read user connection needs to be checked. This is achieved by editing the connectiomn to SQL Server opening Connection page and under Account section  select to Add account. Following the set up process  i connected to server using this new account to observe the Read only access behaviour of this account. 


- [Table of Contents](#table-of-contents)

# Database migration diagram schema
![Azure Project Diagram](https://github.com/HPCurtis/azure-database-migration797/assets/136170998/7b809b53-335a-465c-894d-e08ef6be291e)
