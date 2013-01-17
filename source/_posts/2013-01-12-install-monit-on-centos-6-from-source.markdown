---
layout: post
title: "Install monit on CentOS 6 from source"
date: 2013-01-12 15:01
comments: true
categories: 
---

###Resources  
Monit: [http://mmonit.com/monit/](http://mmonit.com/monit/)   
Wiki: [http://mmonit.com/monit/documentation/monit.html](http://mmonit.com/monit/documentation/monit.html)   
Man:  [http://linux.die.net/man/1/monit](http://linux.die.net/man/1/monit)  
Original: [http://blog.hostonnet.com/installing-monit-on-linux-centos-server](http://blog.hostonnet.com/installing-monit-on-linux-centos-server)
###Installation
Other packes I needed for my box
    yum install pam-devel 
    yum install openssl-devel
 

Get and install Monit
    cd /usr/local/src
    wget http://mmonit.com/monit/dist/monit-5.5.tar.gz
    tar -zxvf monit-5.5.tar.gz
    cd monit-5.5
    ./configure (If this fails, fix as necessary, then re-unpack the tar and try again)
    make
    make install 

 
Setup monitrc
    cp monitrc /etc/
    vi /etc/monitrc  # At the end of monitrc add or uncomment: include /etc/monit.d/*
    mkdir /etc/monit.d

Create the service files (this will repeat for every service you monitor)

    vi /etc/monit.d/apache

    #Now Inside the apache file:
    check process httpd with pidfile /var/run/httpd/httpd.pid
    group apache
    start program = "/etc/init.d/httpd start"
    stop program = "/etc/init.d/httpd stop"
    if failed host 127.0.0.1 port 80 protocol http
     and request "/index.html"
    then restart
    if 5 restarts within 5 cycles then timeout
     
To see more services, [check this out.](http://mmonit.com/wiki/Monit/ConfigurationExamples)
 
Now setup the init.d file
    cp contrib/rc.monit /etc/init.d/monit
    chmod 755 /etc/init.d/monit

You may need to fix the line inside the above file thats pointing at `/usr/bin/monit` to `/usr/local/bin/monit`.
After this is in place you should have the `service monit restart` command available.


To start Monit at boot, edit `vim /etc/rc.d/rc.local` and add in the next line
    /usr/local/bin/monit -d 60 -v -c /etc/monitrc -p /var/run/monit.pid -l /var/log/monit.log

Go ahead and run the above line in the console to see if monit works. If so, a call to `service httpd stop` should cause monit to restart apache.

###Finished

Monit should be all setup. You can check with `service monit status` or `ps aux | grep monit`. 

Another cool feature of monit is the web interface. Go to [http://localhost:2812/](http://localhost:2812/) and enter the username and password form your monitrc file, it should look something like this, feel free to change it: 
    set httpd port 2812
      allow hauk:password
 
###Main Files
    /etc/monitrc - Monit's control file

    /etc/monit.d/* - all services Monit will track

    /etc/init.d/monit - service control file

    /usr/local/src/monit-5.5 - source code

    /usr/local/bin/monit