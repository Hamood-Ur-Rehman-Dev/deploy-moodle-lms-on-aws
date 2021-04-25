# Deploy Moodle LMS on AWS
***Easy Steps To Deploy Moodle LMS on AWS (EC2)***

* GoTo **Services** > **EC2** Console and Click on **Launch Instances**	
    *   Now select any available **Amazon Machine Image (AMI)**, preferably click on “*Free tier only*” checkbox and select ***Red Hat Enterprise Edition***.
    *   Choose **Instance Type t2.medium** 
    *   Click on button Next: Configure Instance Details don’t change anything here
    *   Click on button Next: Add Storage again don’t change anything here
    *   Click on button Next: Add Tags and click on **click to add a name** tag and write Name for instance here.
    *   Click on button Next: **Configure Security Group** and **change already added rule Type** to **All Traffic** rest of columns update automatically. Click on **Review and Launch** and 
    *   Click on Launch, this will prompt you for **Creating Key Pair** and **Click Download Key Pair**
    *   In last Click on **Launch Instance** this will take some time, so wait until **Instance State change to Running** and **Status Check change to 2/2 checks**.
    *   Now connect to your **EC2 instance** using SSH key 
        * *``chmod 400 [YOUR_KEY.pem]``*
        * *``ssh -i ‘[YOUR_KEY.pem]’ ec2-user@path……amazoneaws.com”``*
      
* Connect to Instance and install few things that we need to run Moodle
    *   Switch user to root first 
        * *``sudo su - root``*
    *   Install **php7.2** on OS, and **run php service** and **check php status**
        * *``yum module install php:7.2 -y``*  
        * *``systemctl enable —now php-fpm``*  
        * *``systemctl status php-fpm``*  
    *   Next we need MySQL for that create file 
        *   *``vi /etc/yum.repos.d/mysql-community.repo``* 
        *   and write these lines in it file
            *   *``[mysql57-community]``*
            *   *``name=MySQL 5.7 Community Server``*
            *   *``baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/``*
            *   *``enabled=1``*
            *   *``gpgcheck=0``*
            *   *``[mysql-connectors-community]``*
            *   *``name=MySQL Connectors Community``*
            *   *``baseurl=http://repo.mysql.com/yum/mysql-connectors-community/el/7/$basearch/``*
            *   *``enabled=1``*
            *   *``gpgcheck=0``*
            *   *``[mysql-tools-community]``*
            *   *``name=MySQL Tools Community``*
            *   *``baseurl=http://repo.mysql.com/yum/mysql-tools-community/el/7/$basearch/``*
            *   *``enabled=1``*
            *   *``gpgcheck=0``*
        *  Ref: https://computingforgeeks.com/install-mysql-5-7-on-centos-rhel-linux/
        *  Install **mysql** & **mysql-server**, **start mysql services** and **check mysql status** 
           *  *``yum install mysql mysql-server -y``*
           *  *``sudo systemctl enable --now mysqld.service``*
           *  *``sudo systemctl status mysqld.service``*  
    *   Next we need to **install Web Server**, **Start Web Server** and **Check Status**
        * *``yum install httpd -y``* 
        * *``systemctl start httpd``* 
        * *``systemctl status httpd``* 
*   Now **Download** **Moodle** 
    *   We need **wget** to download so install this first
        * *``yum install wget -y``*
    *   Download Moodle Now (Replace **310** with available stable version)
        * *``wget https://download.moodle.org/download.php/direct/stable310/moodle-latest-310.zip``*
    *   As this is .zip file we need unzip
        * *``yum install unzip -y``*
        * *``unzip moodle-3-10-0.zip``*
    *   Move moodle folder inside web server
        * *``mv moodle /var/www/html/``*  
    *   Give Permission to www folder 
        * *``chmod 777 /var/www/``*  
*   Last step is to get Public IP of this running Instance 
    * Goto you **EC2 instance** and find **Public IPv4 address** 
*   Past this IPv4 in browser **YOUR_IPv4/moodle/install.php**
*   You will see **Forbidden messages** because **firewall** is blocking this, we have to stop firewall 
    *   *``setenforce 0 ``*
*   After stoping firewall refresh webpage **YOUR_IPv4/moodle/install.php** you will see Moodle installation wizard 
*   Moodle need database to be already exist so connect to your EC2 instance and create Database
    *   *``mysql -u root -p``* *(This will prompt you for password simply press Enter)*
    *   *``create database YOUR_DATABASE_NAME;``*
    *   *``\q``* *(to quit from mysql terminal \q)*
*   You still need **Database Drivers** so we install **mysqli**.
    *   *``yum install php-mysqli -y``*
*   In last step of Moodle wizard you prompted to save **config.php** file copy the content prompted and create **config.php file in moodle directory** and past in it.
*   If you not full fill minimum requirement for the moodle will prompt you in my case it prompt me for **xmlrpc, gd, intl, soap, zip** so I install these 
    *   *``yum install php-zip php-gd php-intl php-xmlrpc php-soap -y``*
*   See now your URL is **YOUR_IPv4/moodle** to make it work on only IP do as directed
    *   *``mv /var/www/html/moodle/{.,}* /var/www/html``*
    *   Don’t forget to change in config.php file 
        *   *``$CFG->wwwroot   = 'http://[YOUR_IPv4]’;``*
*   After this all good


## Some Basic Settings Inside Moodle Dashboard
* Change "**post_max_size**", "**upload_max_filesize**" and "**max_execution_time”**, you might need this in future.
  * Find location of **php.ini** you have to Goto **Site Administration** >> **Server** >> **PHP info** >> Under **Loaded Configuration**
    *  *``sudo vi /etc/[PATH_TO_PHP.ini]``*
    *  Search "**post_max_size**", "**upload_max_filesize**" and "**max_execution_time”** and change values as needed
  
* Turn Off guest access
  * Site **Administration** >> **Plugins** >> **Authentication** >> **Manage Authentication**.
  * Set **Guest Login Button HIDE**
  
* **Php.ini** Settings
  * **upload_max_size = 128M**
  * **post_max_size = 128M**
  * **memory_limit = 384M**
  * **max_input_time = 600**
  * **max_execution_time = 300**
  * **max_input_var = 5000**
  
* Custom CSS for Dashboard Settings
  * Site **Administration** >> **Appearance** >>**[YOUR_SELECTED_THEME_NAME]** >> **Advance Settings**
  * In **Raw SCSS**, add the following **.bg-white {background-colour: orange !important;}**
  * Site **Administration** >> **Development** >> **Purge caches**
  * Click **Purge All Caches**
