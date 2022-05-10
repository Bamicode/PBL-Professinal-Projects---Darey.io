## WEB SOLUTION WITH WORDPRESS

## TASK

### 1. To configure storage subsystem for Web and Database servers based on Linux OS
   This focus on practical experience of working with disks, partitions and volumes in Linux.
### 2. To install WordPress and connect it to a remote MySQL database server
   This focus on deploying Web and DB tiers of Web solution
   
### - LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.

Step 1 — Prepare a Web Server

1. Launch an Red Hat Enterprise Linux EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

   Red Hat Enterprise Linux (RHEL)
   
    ![Instances](https://user-images.githubusercontent.com/96090546/167285851-3bb6133b-575f-4bb4-a524-30af3c9add30.PNG)

   DB Server Volume
   ![DB server Vols](https://user-images.githubusercontent.com/96090546/167285831-f2dfdbf0-3537-458c-85fd-ac4176e386dc.PNG)

   Web Server Volume
   ![Web server Vols](https://user-images.githubusercontent.com/96090546/167285833-d5da94d3-9ff3-402e-be74-0b6bbda6e4d0.PNG)


2. Linux terminal to begin configuration

3. Use lsblk command to inspect what block devices are attached to the server. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ to confirm their names which are xvdf, xvdh, xvdg.

         ls blk
      

4. Use df -h command to see all mounts and free space on your server

         df -h

5. Use gdisk utility to create a single partition on each of the 3 disks

         sudo gdisk /dev/xvdf
         
   Then following the procedures.
      
         GPT fdisk (gdisk) version 1.0.3

         Partition table scan:
           MBR: not present
           BSD: not present
           APM: not present
           GPT: not present

         Creating new GPT entries.

         Command (? for help branch segun-edits: p
         Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
         Sector size (logical/physical): 512/512 bytes
         Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
         Partition table holds up to 128 entries
         Main partition table begins at sector 2 and ends at sector 33
         First usable sector is 34, last usable sector is 20971486
         Partitions will be aligned on 2048-sector boundaries
         Total free space is 2014 sectors (1007.0 KiB)

         Number  Start (sector)    End (sector)  Size       Code  Name
            1            2048        20971486   10.0 GiB    8E00  Linux LVM

         Command (? for help): w

         Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
         PARTITIONS!!

         Do you want to proceed? (Y/N): yes
         OK; writing new GUID partition table (GPT) to /dev/xvdf.
         The operation has completed successfully.

      Now run lsblk command to verify the operation was successful;
      
         lsblk
      
   ![Mounted Vols](https://user-images.githubusercontent.com/96090546/167286220-f7c860a9-0a7c-4e50-8122-5241aae1b33f.PNG)

6. Install lvm2 package using: 

         sudo yum install lvm2

      Run sudo lvmdiskscan command to check for available partitions.

         sudo lvmdiskscan

7. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

         sudo pvcreate /dev/xvdf1
         sudo pvcreate /dev/xvdg1
         sudo pvcreate /dev/xvdh1
          
8. Verify that the Physical volume has been created successfully by running sudo pvs

         sudo pvs

      ![sudo pvs](https://user-images.githubusercontent.com/96090546/167286747-dc327aaf-8738-498b-9240-25ebd9e4ff53.PNG)

9. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

         sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1

10. Verify that your VG has been created successfully by running sudo vgs

         sudo vgs

       ![vg create](https://user-images.githubusercontent.com/96090546/167287200-1596cd06-ad10-42b5-9fe4-2c7531458044.PNG)

11. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.


         sudo lvcreate -n apps-lv -L 14G webdata-vg
         sudo lvcreate -n logs-lv -L 14G webdata-vg

12. Verify that your Logical Volume has been created successfully by running sudo lvs

            sudo lvs
            
       ![sudo vgs](https://user-images.githubusercontent.com/96090546/167287492-095e675a-1a30-4a1c-9f09-c63cdde197b3.PNG)

13. Use mkfs.ext4 to format the logical volumes with ext4 filesystem

         sudo mkfs.ext4 /dev/webdata-vg/apps-lv
         
         or use the command:

         sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
         sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

14. Create /var/www/html directory to store website files

         sudo mkdir -p /var/www/html

15. Create /home/recovery/logs to store backup of log data

         sudo mkdir -p /home/recovery/logs

16. Mount /var/www/html on apps-lv logical volume

         sudo mount /dev/webdata-vg/apps-lv /var/www/html/
         
17. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

         sudo rsync -av /var/log/. /home/recovery/logs/
         
18. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)

         sudo mount /dev/webdata-vg/logs-lv /var/log
         
19. Restore log files back into /var/log directory

         sudo rsync -av /home/recovery/logs/. /var/log
         
20. Verify the entire setup

         sudo vgdisplay -v #view complete setup - VG, PV, and LV
         sudo lsblk 

       ![Verify setup](https://user-images.githubusercontent.com/96090546/167289102-f4decde7-321d-420f-bf68-ecaed09d1af6.PNG)

21. Update /etc/fstab file so that the mount configuration will persist after restart of the server
We shall uhe UUID of the device will be used to update the /etc/fstab file.

      sudo blkid
      sudo vi /etc/fstab

   ![blkid](https://user-images.githubusercontent.com/96090546/167289531-6a870b50-efa7-471b-9221-a308b0c41608.PNG)

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes

![etc-fstab update-UUID](https://user-images.githubusercontent.com/96090546/167289705-e0b2476e-3269-49cf-a915-0ef5627ff096.PNG)

22. Test the configuration and reload the daemon

        sudo mount -a
        sudo systemctl daemon-reload
         
Verify your setup by running df -h, output must look like this:

         df -h

![Config test](https://user-images.githubusercontent.com/96090546/167289708-66adbf42-76c6-4681-8f7f-f4df1e73feb0.PNG)

### Step 2 — Prepare the Database Server

### - Launch a second RedHat EC2 instance that will have a role – ‘DB Server’

Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

 Mount volume on the DBServer and confirm running the lsblk command:

      lsblk
      
![DB server lsblk](https://user-images.githubusercontent.com/96090546/167290023-d12b184e-db9c-4827-8605-a03721680907.PNG)

Verify the created volumes running the following commands on the DB Server terminal

      sudo pvs
      sudo lvs
      sudo vgs

![DB S lvs-----](https://user-images.githubusercontent.com/96090546/167290294-91e281f4-da86-471f-aef0-9bbaee839f6d.PNG)

Updated the /etc/fstab UUID and testing the configuration, we have

![Test config-DB serv](https://user-images.githubusercontent.com/96090546/167290612-0fbf1c69-dc78-4a45-9bd5-b03b4c501753.PNG)


### Step 3 — Install WordPress on your Web Server EC2

1. Update the repository

         sudo yum -y update

2. Install wget, Apache and it’s dependencies

         sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
         
3. Now install PHP and it’s depemdencies

         sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
         sudo yum install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
         sudo yum module list php
         sudo yum module reset php
         sudo yum module enable php:remi-7.4
         sudo yum install php php-opcache php-gd php-curl php-mysqlnd
         sudo systemctl start php-fpm
         sudo systemctl enable php-fpm
         sudo systemctl status php-fpm
         sudo setsebool -P httpd_execmem 1
         
4. Start Apache

         sudo systemctl enable httpd
         sudo systemctl start httpd
         sudo systemctl restart httpd
         
Now paste the (Public-ip-address) of the web Server to test the running of the Red Hat Enterprise Linux EC2 instance server.
   
   ![REHL page](https://user-images.githubusercontent.com/96090546/167293087-8849dba7-3630-43d5-bfc0-14675e17ed58.PNG)

5. Installing wordpress on the Web Server

   Download wordpress and copy wordpress to var/www/html

        mkdir wordpress
        cd   wordpress
        sudo wget http://wordpress.org/latest.tar.gz
        sudo tar xzvf latest.tar.gz
        sudo rm -rf latest.tar.gz
        sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
        or use 
        sudo cp -R wp-config-sample.php wp-config.php
     
     Go back to the wordpress directory and copy files to /var/www/html/
     
       sudo cp -R wordpress/. /var/www/html/
       
     Change dir. to /var/www/html

         cd /var/www/html
         
### Step 4 — Install MySQL on your DB Server EC2

      sudo yum update
      sudo yum install mysql-server

Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:

      sudo systemctl restart mysqld
      sudo systemctl enable mysqld
      sudo systemctl status mysqld

### Step 5 — Configure DB Server to work with WordPress

      sudo mysql

      CREATE DATABASE wordpress;
      CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
      GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
      FLUSH PRIVILEGES;
      SHOW DATABASES;
      exit         

   Now goto the Webserver terminal and configure edit the php landing page;
   On the html directory, that is cd /var/www/html
   
         sudo vi wp-config.php
   Edit the database_name, user_name, password, and Host_database (Database Server-ip-address)
   
   ![wp-config](https://user-images.githubusercontent.com/96090546/167357716-bd0b7700-b799-463e-b194-6c75f6bb8306.PNG)
   
   And configure the bind-address
   
   ![bind-addr](https://user-images.githubusercontent.com/96090546/167357543-224636b0-6b4e-4d63-8e0e-5e1defcdae83.PNG)
   
   Now there's need to first disable the default apache homepage. Run the command to do so;
   
         sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup
   Restrart Apache
   
         sudo systemctl restart httpd
         
### Step 6 — Configure WordPress to connect to remote database.
                 
      Configure security group
   
   ![Webserver access only](https://user-images.githubusercontent.com/96090546/167297739-14c94f22-1db6-4ec4-95fa-310ce233b97e.PNG)
   
   Now, run the command to connect to the Database remote server via wordpress on the webserver
   
      sudo mysql -h <my database-ip-address> -u <database_name> -p
   
   ![webserver communication with database server](https://user-images.githubusercontent.com/96090546/167357549-99723315-88fa-4c78-a7ca-dbfb1d817e0c.PNG)
      
Now on the browser, test to install and configure our wordpress installation on the database as show below.

![wordpress1](https://user-images.githubusercontent.com/96090546/167362266-501e0931-0990-4109-ae38-b6a93fa2df5f.PNG)

![wordpress success](https://user-images.githubusercontent.com/96090546/167362264-9590f891-0d38-40d9-8bd4-98fc9ec4d94f.PNG)

![Wordpress homepage](https://user-images.githubusercontent.com/96090546/167362258-3015f410-26f8-44ca-b68a-177985f331bc.PNG)



DONE!!!

