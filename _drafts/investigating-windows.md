---
layout: post
title:  "TryHackMe - Investigating Windows - Writeup!"
# date:   2021-01-08 23:00:59
categories: thm
---


# [Investigating Windows](https://tryhackme.com/room/investigatingwindows) - Starting the box can take some time (Draft)

<hr>
<br>



####  Whats the version and year of the windows machine?
 {% highlight powershell %}
 systeminfo
{% endhighlight%}

####  Which user logged in last?
{% highlight powershell %}
 get-localuser | Where-Object {$_.Lastlogon -ge (Get-Date).AddDays(-10)} | Select-Object Name,Enabled,SID,Lastlogon | Format-List
{% endhighlight %}

####  When did John log onto the system last?
 {% highlight powershell %}
  get-localuser | Where-Object {$_.Name -eq "John"} | Select-Object Name,Enabled,SID,Lastlogon | Format-List
{% endhighlight%}

#### When did Jenny last logon?
{% highlight powershell%}
get-localuser | Where-Object {$_.Name -eq "Jenny"} | Select-Object Name,Enabled,SID,Lastlogon | Format-List
{% endhighlight %}

#### What was the extension name of the shell uploaded via the servers website?

{% highlight powershell%}
ipconfig.exe /all
{% endhighlight%}

#### What IP does the system connect to when it first starts? (I have to clue)


#### What two accounts had administrative privileges (other than the Administrator user)?
{% highlight powershell %}
%> net users Jenny
%> net users John
%> net users Guest
{% endhighlight%}



####  Whats the name of the scheduled task that is malicous.
{% highlight powershell %}
 schtasks.exe /query /fo LIST /v
{% endhighlight %}

But better is to see in the GUI, everything is present there.