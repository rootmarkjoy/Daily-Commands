#### Contact form script:-

```sh
<?php
ini_set("include_path", '/home/username/php:' . ini_get("include_path"));
require_once "Mail.php";

$from = "Sandra Sender <info@domainname.com>";
$to = "Ramona Recipient <test.milesweb@gmail.com>";
$subject = "Hi!";
$body = "Hi,\n\nHow are you?";

$host = "inr05.solidhosting.pro";
$port = "25";
$username = "info@domainname.com";
$password = "]y#o~~=7bu+~";

$headers = array ('From' => $from,
  'To' => $to,
  'Subject' => $subject);
$smtp = Mail::factory('smtp',
  array ('host' => $host,
    'port' => $port,
    'auth' => true,
    'username' => $username,
    'password' => $password));

$mail = $smtp->send($to, $headers, $body);

if (PEAR::isError($mail)) {
  echo("<p>" . $mail->getMessage() . "</p>");
 } else {
  echo("<p>Message successfully sent!</p>");
 }
?>
```
