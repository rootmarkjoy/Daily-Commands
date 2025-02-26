#### You can use this for header: Important: Put the following on your PHP pages that you want to include the content.

```sh
<?php
//at top:
require('header.php'); 
 ?>

 <?php
// at bottom:
require('footer.php');
?>
```

#### You can also include a navbar globaly just use this instead:

```sh
<?php
 // At top:
require('header.php'); 
 ?>
  <?php
// At bottom:
require('footer.php');
 ?>
 <?php
 //Wherever navbar goes:
require('navbar.php'); 
?>
```

#### In header.php:

```sh
<!DOCTYPE html>
 <html lang="en">
 <head>
    <meta charset="utf-8">
 <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
 </head>
 <body>
```
