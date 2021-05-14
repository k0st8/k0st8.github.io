---
layout: post
title:  "TryHackMe - Red Stone One Carat - Writeup!"
date:   2021-05-14 12:06:59
categories: thm
---


# [Red Stone One Carat](https://tryhackme.com/room/redstoneonecarat) - Starting the box can take some time

<hr>




---
<br>

### Set IP 

New starting point set `IP` to be environment variable
{% highlight bash %}
$ echo 'export ip=10.10.112.111' >> .bashrc
$ exit
{% endhighlight %}
Now open new Terminl window and `$ip` is set. Ready to go


### SSH 
Not a typical kind of CTF. Credentials provided right a way `noraj `, but password contians `bu` in it.
{% highlight bash %}
$ grep bu /usr/share/wordlists/rockyou.txt > bu_pass.txt
{% endhighlight %}
So the `hydra` has less work.
{% highlight bash %}
$ hydra -l noraj -P bu_pass.txt ssh://$ip
{% endhighlight %}

![RedStone](/assets/thm/redstonecarat/hydra_noraj.png)

### User is rzsh-ed
But is was way too restricted at least for me.

![RedStone](/assets/thm/redstonecarat/restricted_shell.png)

After some research found the way to get `irb` on the machine

![RedStone](/assets/thm/redstonecarat/irb.png)

### User Flag
It's was easy one, but before created functio to read file.
{% highlight ruby %}
def r_f file
 ### Read file the easy way
 f = File.open("#{file}",'r')
 f.read.each_line do |line|
  print "#{line}"
 end
 return nil
end
{% endhighlight %}
It's better way to print files, in this case, then on the picture. 
![RedStone](/assets/thm/redstonecarat/user_flag.png)

### Priv Esc 
{% highlight bash %}
irb(main):029:0> system 'find / -user root -perm -u=s 2>/dev/null'
/bin/mount
/bin/su
/bin/ping
/bin/fusermount
/bin/umount
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/traceroute6.iputils
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/tmp/bash
=> false
irb(main):030:0>
{% endhighlight %}

Let's see if there are any files for `crontabs`.
![RedStone](/assets/thm/redstonecarat/dummy_sh.png)

Now let's see what is in `dummy.sh`

![RedStone](/assets/thm/redstonecarat/dummy_sh_content.png)

### Finding Roots

The hard staff is starts, connecting to the `port 31547` has not gave me much.
So I’ve tried coping `dash` to privilege escalation... nothing... user `noraj` was coping the file adding sticky bit wouldn’t help.

![RedStone](/assets/thm/redstonecarat/trying_dash.png)

And then after couple hours later... connecting to the port... after much time reading about delimiters of `ruby`

![RedStone](/assets/thm/redstonecarat/copy_bash.png)

At least copy was successful...
![RedStone](/assets/thm/redstonecarat/root_flag.png)


<br>
<hr>

### References
[Restricted Shell escape](https://www.hacknos.com/rbash-escape-rbash-restricted-shell-escape/)

[General Delimited Input](https://www.linuxtopia.org/online_books/programming_books/ruby_tutorial/The_Ruby_Language_General_Delimited_Input.html)

