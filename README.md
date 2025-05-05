# Wordpress Installation

1. Install Apache, MariaDB and PHP

2. Create a database for Wordpress
  a.	`CREATE DATABASE wordpress;`
  b.	`CREATE USER wordpressuser@localhost IDENTIFIED BY 'abc';`
  c.	`GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'abc';`
  d.	`FLUSH PRIVILEGES;`
  e.	Verify it using `mysql -u'wordpressuser' -p 'abc' wordpress`

3. Verify PHP by:
  a. `vi /var/www/html/info.php` and paste `<?php phpinfo(); ?>` and then verify by `localhost/info.php`

4. Verify DB connection via PHP by:
   a. `vi /var/www/html/test.php`
   b. Add below text:
```
<?php
$user = "wordpressuser";
$password = "abc";
$database = "wordpress";
$table = "test_table";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>"; 
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
  c.	Verify DB connection by restarting httpd service and access `localhost/test.php`. This page will display content of “test_table” from “wordpress” database.

5. Install Wordpress:
  a. `wget http://wordpress.org/latest.tar.gz`
  b. `tar xvzf latest.tar.gz`
  c. `rsync -avP ~/wordpress/ /var/www/html/`
  d. `mkdir /var/www/html/wp-content/uploads`
  e. `useradd apache` and `groupadd apache`
  f. `chown -R apache:apache /var/www/html/*`
  g. `cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php`
  h. add database, user, and password in `/var/www/html/wp-config.php` file
  i. `systemctl restart httpd.service`
  j. Access your VM IP from browser by `localhost:80` and continue with installation.
  k. Use this command if key generation is required `curl -s https://api.wordpress.org/secret-key/1.1/salt/`

6. In case of remote database:
   a. `vim /etc/selinux/config`
   b. Change `SELINUX=disabled` and run `setenforce 0` and verify using `sestatus`.
   c. Run `systemctl stop firewalld` and `systemctl disable firewalld`.
   d. Edit privileges from database host server by:
     i.	`CREATE USER wordpressuser@'host_db_ip' IDENTIFIED BY 'abc';`
    ii.	`GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@'host_db_ip' IDENTIFIED BY 'abc';`
   iii.	`FLUSH PRIVILEGES;`
   iv. Install MariaDB Client on Wordpress server and verify DB connectivity.
  e. Edit `/var/www/html/wp-config.php` and add DB, User and IP created in DB host
  f. Run `systemctl restart httpd`
