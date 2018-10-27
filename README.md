## Devops/Sysadmin Tasks ##


### **Project Description:** ###
The server should use Nginx on an AWS micro instance.
Install Drupal 7 and CiviCRM on this server in a secure fashion with SSL certificate from https://letsencrypt.org/. You may wish to research what Nginx configurations are required to make both Drupal AND CiviCRM secure. Please install CiviCRM and Drupal in different databases as this is considered best practice.

**Important notes:**   
- Infrastructure Automation with Ansible was applied to this project.
- [Create a AWS free tier account before start:](https://portal.aws.amazon.com/billing/signup#/start)
- [Create EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
- Link to the site: [https://jcontti.ga/](https://jcontti.ga/)
- Site user: admin
- Site pass: admin
- Backup is doing daily at 4pm and not in other way because is a non-production environment and other way it is considered unnecessary

**Goals:**
1. The infrastructure is setup in a cloud environment using AWS(EC2)
2. Configure Drupal to have a private files folder. Ensure this is secure within your NGINX configuration.
3. Ensure that the Nginx configuration is secure for Drupal and CiviCRM including any other files upload folders.
4. Modify the CiviCRM settings file so that the CiviCRM extension directory is set using settings files variables
5. Install all necessary packages required for a server to run Drupal 7/civicrm (mysql, php, nginx, etc.)
6. Install Drush to the server for the appropriate user
7. Install CiviCRM and Drupal databases to separate database instances
8. Modify the Drupal settings files so that Drupal views can use the CiviCRM database
9. Backup the sites and databases.

**Specifications:**
 - Ubuntu Server 16.04 64-bit
 - Nginx
 - php
 - mysql
 - CiviCRM
 - Drupal 7 
 - Drush


**Project Plan:**


#### Following is the rough plan.####


| Day    | Tasks   |
| ------ |---------|
| Day 1  | Analyze problems and goals.|
| Day 2  | Revise Drupal, CiviCRM and Nginx concepts and requirements.|
| Day 3  | Write Ansible playbooks to setup the EC2 environment.|
| Day 4  | Install all packages/dependencies with Ansible.|
| Day 5  | Setup CiviCRM and Drupal databases to separate database instances.|
| Day 6  | Setup Drupal and CiviCRM.|
| Day 7  | Install schedules configurable backups cronjob to AWS S3.|
| Day 8  | Test complete setup multiple times.|



####Folder structure:####
<pre><span><font color="#3465A4">ansible/</font></span>
├── ansible.cfg
├── <font color="#8AE234"><b>master.yml</b></font>
└── <font color="#729FCF"><b>roles</b></font>
    ├── <font color="#729FCF"><b>aws</b></font>
    │   ├── <font color="#729FCF"><b>defaults</b></font>
    │   │   └── main.yml
    │   └── <font color="#729FCF"><b>tasks</b></font>
    │       └── main.yml
    ├── <font color="#729FCF"><b>backup</b></font>
    │   ├── <font color="#729FCF"><b>defaults</b></font>
    │   │   └── main.yml
    │   ├── <font color="#729FCF"><b>tasks</b></font>
    │   │   └── main.yml
    │   └── <font color="#729FCF"><b>templates</b></font>
    │       ├── backup.sh.j2
    │       └── config.j2
    ├── <font color="#729FCF"><b>civicrm</b></font>
    │   ├── <font color="#729FCF"><b>files</b></font>
    │   │   ├── civicrm.drush.inc
    │   │   └── settings_php_civicrm_update.txt
    │   └── <font color="#729FCF"><b>tasks</b></font>
    │       └── main.yml
    ├── <font color="#729FCF"><b>dependencies</b></font>
    │   └── <font color="#729FCF"><b>tasks</b></font>
    │       └── main.yml
    └── <font color="#729FCF"><b>drupal</b></font>
        ├── <font color="#729FCF"><b>defaults</b></font>
        │   └── main.yml
        ├── <font color="#729FCF"><b>tasks</b></font>
        │   └── main.yml
        └── <font color="#729FCF"><b>templates</b></font>
            ├── default
            ├── nginx.conf
            └── template-domain
</pre>


**Step by step:**


1. Setup Ansible in a local machine
   - [Install Ansible](https://docs.ansible.com/ansible/2.5/installation_guide/intro_installation.html)
   - [Install Pip](https://www.rosehosting.com/blog/how-to-install-pip-on-ubuntu-16-04/)
   - To be able to use the AWS via Ansible we are going to use boto library so make sure you have that installed. To install it you just need to run this command in a local machine:   
    `pip install boto`
   - Once boto is installed you need to setup your AWS credentials, this is one of many examples:  
   [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)  

2. Change directory to the directory holding the ansible yml file, in this case is:  
   `cd ~/ansible-drupal-app/ansible/`  


3. Once you have completed all the previous steps, you can run the playbook below.  
`ansible-playbook master.yml -vvv`   
This playbook calls 5 roles:  
   - **aws:** to Install and Setup AWC EC2
      - Create a security group
      - Create EC2 instance
      - Add host in memory
      - Wait a few second to continue with the others roles 
   - **dependencies:** to setup all packages and dependencies (mysql,php,nginx, etc.)
      - Install Python
      - Install Nginx
      - Install PHP and libs
      - Install MYSQL-Server
      - Install PHP-MySQL
      - Install other depends
      - Create Certificate 
      - Add crontab to renew certificates
      - PHP Configuration
   - **drupal:** to install and Setup Drupal
      - Add Drupal user
      - Install DRUSH
      - Create Drupal DB
      - Create Drupal Mysql User
      - Download and install Drupal
      - Copy config files
      - Create private directory
      - Restart Nginx
   - **civicrm:** to Install and Setup CiviCRM
      - Create CiviCRM DB
      - Create CiviCRM Mysql User
      - Reload privilege tables allowing select
      - Download and install CiviCRM for Drupal7
      - Copy files and set permissions
      - Views3 Integration
   - **backup:**
      - Install pip boto 
      - Install AWS Cli.
      - Ensure .aws/ and .cron/ dirs exists
      - Copy access and secret key
      - Copy backup script
      - Configure backup in s3bucket with cronjob( in a month would take 7GB+ in s3bucket)
        
4. Once all task are complete you can go and check the website, in my case: [https://jcontti.ga](https://jcontti.ga)

5. It's important to check the securty, in my case I used [SSLlabs](https://www.ssllabs.com/ssltest/)
and my result was A+





**References:**

- CiviCRM
    - [What is CiviCRM](https://docs.civicrm.org/user/en/latest/introduction/what-is-civicrm/)
    - [Requirements](https://docs.civicrm.org/sysadmin/en/latest/requirements/)
    - [Installing CiviCRM for Drupal7](https://docs.civicrm.org/sysadmin/en/latest/install/drupal7/)
    - [Views3 Integration](https://wiki.civicrm.org/confluence/display/CRMDOC/Views3+Integration)
    - [Override CiviCRM Settings](https://docs.civicrm.org/sysadmin/en/latest/customize/settings/)
- Drupal
    - [Before installation](https://www.drupal.org/docs/7/install/before-installation)
    - [Create the database](https://www.drupal.org/docs/7/install/step-2-create-the-database)
    - [Drush: General documentation](https://docs.drush.org/en/master/install/)
- Let’s Encrypt / Nginx 
    - [Let’s Encrypt: How it works](https://letsencrypt.org/how-it-works/)
    - [Setting up Nginx](https://www.nginx.com/blog/setting-up-nginx/)
    - [Nginx webserver security guide](https://geekflare.com/nginx-webserver-security-hardening-guide/)
    - [How to install Nginx on Ubuntu 16.04](https://www.rosehosting.com/blog/how-to-install-nginx-on-ubuntu-16-04/)
- Others
    - [Using Ansible to create AWS instances](https://www.tivix.com/blog/using-ansible-create-aws-instances)
    - [Manage Cron with Ansible](https://docs.ansible.com/ansible/2.5/modules/cron_module.html)
    - [s3 Commands with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/using-s3-commands.html)
    - [Free domains with Freenom](https://www.freenom.com)
