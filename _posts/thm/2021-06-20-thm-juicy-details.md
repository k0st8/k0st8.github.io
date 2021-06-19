---
layout: post
title:  "TryHackMe - Juicy Details - Writeup!"
date:   2021-06-20 20:56:59
categories: thm
---


# [Juice Details](https://tryhackme.com/room/juicydetails) - Download Task files

<hr>

### Reconnaissance
What tool did the attacker use? (Order by the occurence in the log)
{% highlight ruby %}
cat access.log | cut -d'"' -f6-|sort -u
{% endhighlight %}
What endpoint was vulnerable to a brute-force attack?
{% highlight ruby %}
cat access.log | grep -i hydra | grep 200
{% endhighlight %}
What endpoint was vulenrable to SQL injection?
{% highlight ruby %}
cat access.log | grep -i sqlmap | egrep ' 200 '
{% endhighlight %}
What parameter was used for the SQL injection?
{% highlight ruby %}
cat access.log | grep -i sqlmap | egrep ' 200 ' | egrep '\?.\='
{% endhighlight %}
What endpoint did the attacker try to use to retrive files? 
{% highlight ruby %}
cat access.log | egrep -i 'POST|GET \/\w{3}'
{% endhighlight %}
### Stolen data
What section of the website did the attacker use to scrape user email addresses?

{% highlight ruby %}
cat access.log | grep GET| cut -d'"' -f2|cut -d' ' -f1-2| sort -u| grep -i review
{% endhighlight %}

Was their brute-force attack successful? If so, what is the timestamp of the successful login? (Yay/Nay, 11/Apr/2021:09:xx:xx +0000)

{% highlight ruby %}
cat access.log | grep -i hydra | grep 200| cut -d' ' -f4-5
{% endhighlight %}

What user information was the attacker able to retrieve from the endpoint vulnerable to SQL injection?

{% highlight ruby %}
cat access.log | grep passw
{% endhighlight %}
What files did they try to download from the vulnerable endpoint? (endpoint from the previous task, question #5)
{% highlight ruby %}
cat vsftpd.log | grep -i download| cut -d'"' -f4|tr -d '/'|sort | tr '\n' ','
{% endhighlight %}
What service and account name were used to retrieve files from the previous question? (service, username)
{% highlight ruby %}
cat vsftpd.log | grep -i login
{% endhighlight %}

What service and username were used to gain shell access to the server? (service, username)
{% highlight ruby %}
cat auth.log | grep -i accepted
{% endhighlight %}