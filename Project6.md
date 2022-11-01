# Implementing a web solution with wordpress #
>_ *Documentation for project implementation*_

*Lauch an instance for webserver and create 3 volumes*

* Inspect the created volumes
sudo lsblk

![Screenshot from 2022-10-27 10-15-32](https://user-images.githubusercontent.com/106885875/199199968-50a10858-ad4f-4eb7-9df4-2c6ec1df1abe.png)

* inspect all configured partitions on each of the 3 disk volumes
sudo lsblk

![Screenshot from 2022-10-27 10-18-22](https://user-images.githubusercontent.com/106885875/199200505-eea22bbd-d5df-4604-9aee-6887ae203a26.png)

![Screenshot from 2022-10-27 10-20-15](https://user-images.githubusercontent.com/106885875/199201057-b065a5ec-b3a3-40b9-870b-8e5533ed742f.png)

* To verify entire volume setup
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk

![Screenshot from 2022-10-27 10-35-25](https://user-images.githubusercontent.com/106885875/199201750-70766f41-b34d-496b-9e78-0e5437da178f.png)

* format the logical volumes with ext4 filesystem using mkfs -t ext4:-

sudo mkfs -t ext4 /dev/database-vg/apps-lv
sudo mkfs -t ext4 /dev/database-vg/logs-lv

* Create /var/www/html directory to store website files
sudo mkdir -p /var/www/html

* Create /home/recovery/logs to store backup of log data
sudo mkdir -p /home/recovery/logs

* Mount /var/www/html on apps-lv logical volume
sudo mount /dev/database-vg/apps-lv /var/www/html/

* Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
sudo rsync -av /var/log/. /home/recovery/logs/

* Restore log files back into /var/log directory
sudo rsync -av /home/recovery/logs/. /var/log

# Update the /etc/fstab file (required so that mount configuration will persist after restart of the server)

sudo blkid 

![Screenshot from 2022-11-01 10-31-35](https://user-images.githubusercontent.com/106885875/199204106-683acb0f-50dd-4b18-bc64-2ba0d0e29f74.png)

*The UUID of the device will be used to update the /etc/fstab file*

sudo vi etc/fstab

![Screenshot from 2022-11-01 10-36-27](https://user-images.githubusercontent.com/106885875/199204552-0a3aaf38-0552-402a-8d7c-c46f7d198f5d.png)

* test the confgi & reload:
sudo mount -a
sudo systemctl daemon-reload
 
* Verify your setup by running df -h
sudo df -h

![Screenshot from 2022-11-01 10-38-48](https://user-images.githubusercontent.com/106885875/199204903-5438e044-db97-47ca-9406-cb51b6520523.png)

# Step 2 — Prepare the Database Server #

* Note:
*Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.*


# Step 3 — Install WordPress on your Web Server EC2 #

Update the repository, I
Install wget, Apache and it’s dependencies, 
Start Apache, 
Install PHP and it’s depemdencies & 
Restart Apache

![Screenshot from 2022-10-27 11-09-32](https://user-images.githubusercontent.com/106885875/199206349-4c00fc45-69b2-4c8e-9917-2854be4bcbdf.png)

![Screenshot from 2022-10-27 11-09-20](https://user-images.githubusercontent.com/106885875/199206406-56a5b021-066e-4f3d-9430-89c575cf77b4.png)

Download wordpress and copy wordpress to var/www/html
Configure SELinux Policies

# Step 4 — Install & Start MySQL on your DB Server EC2 #
sudo yum update
sudo yum install mysql-server

sudo systemctl restart mysqld
sudo systemctl enable mysqld

# Step 5 — Configure DB to work with WordPress #

![Screenshot from 2022-11-01 10-57-29](https://user-images.githubusercontent.com/106885875/199208185-c52ae5d9-6ccd-45c6-8bc4-a8222e559509.png)


# Step 6 — Configure WordPress to connect to remote database #

sudo mysql -u seyimeg -p -h 172.31.89.92
  
![Screenshot from 2022-10-27 12-38-23](https://user-images.githubusercontent.com/106885875/199208987-a8ac39fa-a99b-4be4-9601-85e5e770ae1c.png)


![Screenshot from 2022-10-27 11-50-55](https://user-images.githubusercontent.com/106885875/199208847-8149f4ef-6dce-4569-b6a2-f957cade9994.png)

![Screenshot from 2022-11-01 09-32-30](https://user-images.githubusercontent.com/106885875/199209083-b65d44cd-df42-4b78-9dd2-7d2bd285c46e.png)

![Screenshot from 2022-11-01 09-32-36](https://user-images.githubusercontent.com/106885875/199209817-9b3f50a4-da7f-4bc4-a6aa-e629cf6f42ba.png)
