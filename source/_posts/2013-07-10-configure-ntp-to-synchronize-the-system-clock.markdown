---
layout: post
title: "Configure NTP to synchronize the system clock"
date: 2013-07-10 15:24
comments: true
categories: [Linux/Unix] 
---

A problem that makes me uncomfortable while I invoking the JD service api to get the categories of the ware items. I spent much time to search the source of the exception, in the end, which is invalid timestamp because the system clock of our test server was not exact. So I need to resolve it by sysnchronizing the time.

![synchronize-time]

<!-- more -->

Login as root user:
install ntp
```
$yum install ntp
```
turn on service
```
$chkconfig ntpd on
```
synchronize the system clock with pool.ntp.org server
```
$ntpdate pool.ntp.org
```
start the ntp
```
$/etc/init.d/ntpd start
```

[synchronize-time]: /images/blog/linux/synch-time.gif
