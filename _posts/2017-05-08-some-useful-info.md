---
layout: post
title:  "Some useful info"
date:   2017-05-08 1:46:30
categories: jekyll update
---
# Some useful info

## Restore deleted or changed files from TimeBack Machine
If you want to restore from TimeBack machine some hidden folders or files:

```
$ defaults write com.apple.finder AppleShowAllFiles TRUE
``` 
after that
 
```
$ killall Finder
```

and to return this all things 

```
$ defaults write com.apple.finder AppleShowAllFiles FALSE  
```

and 

```
$ killall Finder
```

----

## Need to Run locate on Mac
Comand to updatedb is different from the Linux

```
$ sudo /usr/libexec/locate.updatedb
```
and you can run your locate quires

Example to run the quires:

```
$ locate *.jpg
```
or ignore the case 

```
$ locate -i *.png
```

## Some commands to find and execute
Go find them...

Find something that is file type and name bad-file.txt and remove it without asking...

```
$ find . -type f -name "bad-file.txt" -exec rm -f \{};
```
need to find multiple file in row same context as before find and remove without asking.
 
``` 
find . -type f -name "bad-file.txt" -o -name "bad-file2.txt" -exec rm -f \{};
```