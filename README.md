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
   `<?php
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
}`
