# -LAMP-Server-CentOS-7  (Apache 2.4.6 + PHP 7.0.2 + MariaDB 5.5.44)

ในการติดตั้ง Web Server ในปัจจุบันสามารถทำได้หลากหลายวิธี ไม่ว่าจะเป็นการ
เว็บเซิร์ฟเวอร์ที่นิยมใช้งานในปัจจุบัน อาทิ

- Apache HTTP เหมาะสำหรับใช้เขียนภาษา PHP
- Internet Information Services (IIS) เหมาะสำหรับใช้เขียนภาษา ASP.NET
- Apache Tomcat เหมาะสำหรับใช้เขียนภาษา Servlet, JSP ที่พัฒนาจากภาษา JAVA

ส่วนเว็บเซิร์ฟเวอร์แพ็กเกจที่นิยมใช้งาน อาทิ

- AppServ
- XAMPP
- WAMP
- easyPHP
LAMP ย่อมาจาก Linux, Apache, MySQL, PHP
โดยขั้นตอนการติดตั้ง LAMP Server ด้วยตนเองมีขั้นตอนดังต่อไปนี้

ติดตั้ง Apache HTTPd 

 ติดตั้ง Apache on CentOS 7
yum install -y httpd

 สต๊าทบริการและตั้งค่าให้ทำงานตอนบู๊ตระบบ
systemctl start httpd
systemctl enable httpd


กำหนดให้ apache สามารถใช้งานผ่าน firewall ได้ (กรณีเปิดใช้งาน firewalld)
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload


 ทดสอบเว็บเซิฟร์เวอร์จากเครื่องลูกข่าย
http://IP_Address/
เช่น
http://172.16.137.62/
--STEP2: ติดตั้งฐานข้อมูล MariaDB or MySQL
1. ติดตั้งฐานข้อมูล MariaDB
yum install mariadb-server mariadb -y
2. สต๊าทบริการและตั้งค่าให้ทำงานตอนบู๊ตระบบ
systemctl start mariadb
systemctl enable mariadb
3. กำหนดรหัสผ่านให้ MariaDB
mysql_secure_installation 
/usr/bin/mysql_secure_installation: line 379: find_mysql_client: command not found
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!
In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.
Enter current password for root (enter for none): Enter ## กด enter เพื่อเซ็ตรหัาผ่านครั้งแรก
OK, successfully used password, moving on...
Set root password? [Y/n] y ## กำหนดรหัสผ่าน 
New password: ***** ## รหัสผ่าน 
Re-enter new password: ***** ## ยืนยันรหัสผ่าน 
Password updated successfully!
Reloading privilege tables..
... Success!
By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them. This is intended only for testing, and to make the installation
go a bit smoother. You should remove them before moving into a
production environment.
Remove anonymous users? [Y/n] y ## ลบแอคเคาต์ผู้ใช้ภายนอกไม่ให้เข้าระบบ 
... Success!
Normally, root should only be allowed to connect from 'localhost'. This
ensures that someone cannot guess at the root password from the network.
Disallow root login remotely? [Y/n] y ## ไม่อนุญาติให้เข้ามาจากโดเมนภายนอก 
... Success!
By default, MariaDB comes with a database named 'test' that anyone can
access. This is also intended only for testing, and should be removed
before moving into a production environment.
Remove test database and access to it? [Y/n] y ## ลบฐานข้อมูลตัวอย่าง 
- Dropping test database...
... Success!
- Removing privileges on test database...
... Success!
Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.
Reload privilege tables now? [Y/n] y ## ให้ฐานข้อมูลใช้งานได้ตอนนี้ 
... Success!
Cleaning up...
All done! If you've completed all of the above steps, your MariaDB
installation should now be secure.
Thanks for using MariaDB!
4. กำหนดให้ apache สามารถใช้งานผ่าน firewall ได้ (กรณีเปิดใช้งาน firewalld)
firewall-cmd --permanent --zone=public --add-service=mysql
systemctl restart firewalld
5. ทดสอบเข้าใช้งาน
mysql -u root -p
Enter password: ***** ## รหัสผ่านฐานข้อมูล 
Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MariaDB connection id is 15
Server version: 5.5.44-MariaDB MariaDB Server
Copyright (c) 2000, 2015, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MariaDB [(none)]>
------------- Note-----------------------------------------------
กรณีต้องการใช้งาน MYSQL (ไม่ได้เป็นค่า Default) ให้ติดตั้งดังนี้
rpm -Uvh http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
yum install mysql-server
systemctl start mysqld.service
mysql_secure_installation
firewall-cmd --permanent --zone=public --add-service=mysql
firewall-cmd --reload
---------------------------------------------------------------
--STEP3: ติดตั้งตัวแปลภาษา PHP 
1. ติดตั้ง PHP 7
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
yum install -y php70w
yum install -y php70w-opcache php70w-xml php70w-soap php70w-xmlrpc php70w-mbstring php70w-json php70w-gd php70w-mcrypt
2. ตรวจสอบเวอร์ชั่น PHP
# php -v
PHP 7.0.2 (cli) (built: Jan 9 2016 14:00:11) ( NTS )
Copyright (c) 1997-2015 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2015 Zend Technologies
with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2015, by Zend Technologies
3. ตรวจสอบแพ็กเกจที่ติดตั้ง และโมดูล php
yum search php70w-
4. รีสต๊าท Apache
systemctl restart httpd
5. ทดสอบสคริปต์ php
vi /var/www/html/info.php
<?php phpinfo(); ?>
6. ทดสอบจากเครื่องลูกข่าย
http://IP_Address/info.php
เช่น
http://172.16.137.62/info.php
--STEP4: ติดตั้ง phpMyAdmin 
1. ติดตั้ง phpMyAdmin
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y phpMyAdmin
2. ปรับแต่งไฟล์คอนฟิก โดยกำหนดไอพีที่อนุญาติให้เข้าใช้งาน
vi /etc/httpd/conf.d/phpMyAdmin.conf
[...]
Alias /phpMyAdmin /usr/share/phpMyAdmin
Alias /phpmyadmin /usr/share/phpMyAdmin
AddDefaultCharset UTF-8
# Apache 2.4
Require ip 127.0.0.1 172.16 192.168 
Require ip ::1
# Apache 2.2
Order Deny,Allow
Deny from All
Allow from 127.0.0.1 172.16 192.168 
Allow from ::1
# Apache 2.4
Require ip 127.0.0.1 172.16 192.168 
Require ip ::1
[...]
3. ทำการรีสต๊าท Apache
systemctl restart httpd
4. ทดสอบเข้าใช้งานจากเครื่องลูกข่าย
http://IP_Address/phpMyAdmin/
เช่น
http://172.16.137.62/phpMyAdmin/
