---
title: EBM Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  #- <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://tumuvamsi.github.io/slate/vebm_3.1.html'>Vicom Enterprise Balckout Manager Documentation</a>

includes:
 # - errors

search: true
---


# Introduction
Intro: vEBM is an easy-to-use planned maintenance scheduling software that has built-in capability to help efficiently plan and schedule maintenance to suppress unwanted noise. vEBM’s feature rich user-friendly web-interface lets user to create/edit/delete Maintenance schedules quickly for event suppression. It also provides tight integration with various service desk applications. 

# Key Features 
* User friendly Calendar Interface with time zone support. 
* Asset upload from various sources 
* Ready to search assets while creating schedules 
* LDAP Authentication 
* Notification on creation and daily information Mails 
* Bulk CI Maintenance via CSV 
* REST API / WEB SERVICES for third party integrations 
* Simple export features for reporting purpose.  
* Regularly Sync the Asset data and User data (LDAP) 

# Pre-requisites  
## Hardware 
* RAM 6GB Minimum. (Recommended 8GB) 
* Processor 2-CPU system with 3.0 GHz (or greater) CPUs Minimum (4-CPU system with 3.0 GHz (or greater) CPUs) 
* Installation Directory must have 30 GB minimum space 

## Operating System 
* **CentOS7** 
* **RHEL**

## Software 
* Java 1.7 or Later 
* MySQL 5.7 (For standalone) 
* MariaDB (For HA) 

# Installation
## Standalone-Installation  
### Data Base  
1. Download and add the repository.
</br>
**`sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm`**
</br>
2. Install MySQL 5.7 package 
</br>
**`yum install mysql-community-server`**
<p></P>

### Starting MySQL 
1. After the installation is completed, to enable and start the MySQL service</br>
**`sudo systemctl enable mysqld`**</br> 
**`sudo systemctl start mysqld`** </br>
2. Check the MySQL service status by typing</br> 
**`sudo systemctl status mysqld`**</br>

### Secure MySQL 
1. Get the temporary password for SQL with by running below command</br> 
**`sudo grep 'temporary password' /var/log/mysqld.log`**</br>

> The output should look something like this 

```shell
2018-05-23T10:59:51.251159Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: q&0)V!?fjksL
```
<aside class="notice">
(Make note of the password, because the next command will ask you to enter the temporary root password)
</aside>
</br>
2. Run the<span>&nbsp;</span> mysql_secure_installation command 
**`sudo mysql_secure_installation`**

> The output should look something like this

```shell
$ Securing the MySQL server deployment. 
 
$ Enter password for user root: 
```
</br>
3. After entering the temporary password, you will be asked to set a new password for user root. The password needs to be at least 8-character long and to contain at least one uppercase letter, one lowercase letter, one number and one special character. 

> The output should look something like this

```shell
The existing password for the user account root has expired. Please set a new password. 
 
New password: 
 
Re-enter new password: 
```
<aside class="notice">
Make note of the new password for connecting to the database
</aside>
</br>
4. The script will also ask you to remove the anonymous user, restrict root user access to the local machine and remove the test database. You should answer “Y” (yes) to all questions

### Run the schema 
1. create the ebm schema and table with below command </br>
**`mysqldump -u username -p database_name > data-dump.sql`**</br>
2. Enter Password on prompt.</br>

### vEBM Server Installation 
1. Create ebmuser and make sure he has the permission for /opt directory </br>
2. Un-tar the vEBM_v3.x.tar package under /opt </br>
3. tar -xvf vEBM_v3.x.tar 

## HA Installation

1. Will be update Later ...

#Post Installation
## Configure property file
1. Go to /opt/vebm/webapps/VicomEBM-3.0/WEB-INF/classes/</br>
2. Take a backup of application.properties . Run below command</br>
**`cp  application.properties  application.properties.original`**</br>
3. Open the application.properties file</br>
**`vi application.properties`**</br>
4. Update the properties as given below table </br></br>

Property       | Description        | Value
-------------- | ------------------ | ------------------
spring.datasource.password | MySQL password which was created at step 4 of 3.3 | MySql password
spring.mail.host | SMTP Host name | SMTP Host name 
spring.mail.port | Secured SMTP port | 587 (by default) 
spring.mail.username | vEBM Service account mail Id | N/A 
spring.mail.password | vEBM Service account mail Id password | N/A 
spring.mail.from | vEBM Service account mail Id | N/A 
spring.mail.to | vEBM Mail recipients  | N/A 
mail.sentFlag | Email Sent flag to enable or disable Email Notifications | True (by Default) </br>False (for disable)
indexing.indexDirectory | Directory to save the uploaded asset indexes | Directory to save the uploaded asset indexes 
indexing.relational | While creating the schedules this enables the relation of parent child  | True (by Default)</br> False (for disable)  
indexing.resolverGroup | Exact Name of Support group column which uploaded as part of asset upload provides the group-based search  | Empty (by Default)</br>Support group/Resolver group column name of asset data (for enable) 
afterSchedule | Describes the behavior of events after schedule maintenance . For delete events its ‘1’ for unsuppress its ‘0’ | 1/0
assetconfig.cron.expression | Frequency of Regular asset Sync. From the asset upload configuration page details | Every day mid night (By default) 
ldap.user.config.cron.expression | Frequency of Regular LDAP user data sync. From the LDAP  configuration page details | Every day mid night (By default) 
suppressColumn | Column of Alert to update when the schedule active  | SuppressEscl (By default) 
suppressFlag | Value in the column that indicate the alert is suppressed | 6 (By default) 
unsuppressFlag | Value in the column that indicate the alert is un suppressed | 0 (By default) 

## Housekeeping cron jobs

> Below cron jobs used for remove archived log files of 10 days old

```shell
30 2 * * * find /opt/tomcat/logs/ -type f -name "catalina.20[0-9][0-9]-[0-9][0-9]-[0-9][0-9].log" -mtime +10 -delete 

30 2 * * * find /opt/tomcat/logs/ -type f -name "localhost_access_log.20[0-9][0-9]-[0-9][0-9]-[0-9][0-9].txt" -mtime +10 -delete 

30 2 * * * find /opt/tomcat/logs/ -type f -name "localhost.20[0-9][0-9]-[0-9][0-9]-[0-9][0-9].log" -mtime +10 -delete 

30 2 * * * find /opt/tomcat/logs/ -type f -name "manager.20[0-9][0-9]-[0-9][0-9]-[0-9][0-9].log" -mtime +10 -delete

```
## Log rotation for Catalina.out

### Create a file called tomcat
/etc/logrotate.d/tomcat
### Copy the following contents into this tomcat file
/opt/vebm/logs/catalina.out {

  copytruncate

  daily

  rotate 7

  compress

  missingok

  size 200M

}
Here's what each component in the file means: 

 daily - rotates the catalina.out daily
 rotate – keeps up to 7 log files
 compress – compresses the rotated files
 size – rotates if the size of catalina.out is bigger than 200MB
 
No Restart require.

![Benjamin Bannekat](imgs/vEBM_img1.jpg) 
