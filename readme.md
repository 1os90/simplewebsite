# مشروع ويب بسيط باستخدام PHP وPostgreSQL

## المقدمة
هذا مشروع ويب بسيط يتضمن Backend باستخدام PHP وقاعدة بيانات PostgreSQL. يتم استضافته باستخدام Nginx ويمكن تحويله إلى HTTPS باستخدام SSL.

## المتطلبات
- PHP
- PostgreSQL
- Nginx
- Git

## خطوات الإعداد

### 1. إنشاء قاعدة بيانات PostgreSQL
قم بفتح سطر الأوامر الخاص بقاعدة البيانات وأنشئ قاعدة بيانات جديدة:
```sql
CREATE DATABASE mydatabase;

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
### 2. إعداد Backend بـ PHP
قم بإنشاء ملف `index.php` بالمحتوى التالي:
```php
<?php
$host = "localhost";
$db = "mydatabase";
$user = "username";
$pass = "password";

try {
    $pdo = new PDO("pgsql:host=$host;dbname=$db", $user, $pass);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    
    $stmt = $pdo->query("SELECT * FROM users");
    $users = $stmt->fetchAll(PDO::FETCH_ASSOC);
    
} catch (PDOException $e) {
    echo "Connection failed: " . $e->getMessage();
}
?>
<!DOCTYPE html>
<html>
<head>
    <title>Users</title>
</head>
<body>
    <h1>User List</h1>
    <ul>
        <?php foreach ($users as $user): ?>
            <li><?php echo htmlspecialchars($user['name']); ?> - <?php echo htmlspecialchars($user['email']); ?></li>
        <?php endforeach; ?>
    </ul>
</body>
</html>

3 ## إعداد Nginx
server {
    listen 80;
    server_name simplewebsite.com;

    root /path/to/your/project;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock; # تأكد من أن الإصدار صحيح
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
