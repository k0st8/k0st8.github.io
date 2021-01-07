---
layout: post
title:  "Nginx intro"
date:   2017-05-09 1:46:30
categories: Tech
---

# Nginx intro

#### It goes without saying that nginx is hard to configure, after couple of years with Apache

Had the famous


```
$ sudo apt-get install nginx
```

you need to remove apache, before it starts to work as expected...

# 502 Bad Gateway

change this line in order to make it work

```
listen = /var/run/php5-fpm.sock
```
to this line 

```
listen = 127.0.0.1:9000
```