---
layout: post
title: Ubuntu configure DNS for development enviroment
permalink: /490
tags: [admin, administration, bind, bind9, dhcp, dns, host, hosting, resolv, ubuntu]
----

Really hate to add domains to /etc/hosts, then to apache virtual hosts etc
only for get enviroment for work.


My idea is to configure DNS to resolve any subdomains so i will never ever
need to edit hosts file.


Firs of all dns must be installed, i used

    
    <code>sudo tasksel</code>


then choose DNS server and press OK.


It may be installed via:

    
    <code>sudo apt-get install bind9</code>


So now we have own local DNS server, and need to add domain

    
    <code>sudo nano /etc/bind/named.conf.local</code>


And add to it something like this:

    
    <code>zone "x51rl.example.org.ua" {
            type master;
            file "/etc/bind/db.x51rl.example.org.ua";
    };</code>


Now we need file to describe this zone

    
    <code>sudo cp /etc/bind/db.local /etc/bind/db.x51rl.example.org.ua</code>


Now edit this file, replace localhost to x51rl.example.org.ua, and add
wildcard zone

    
    <code>mac@x51rl:/$ cat /etc/bind/db.x51rl.example.org.ua 
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL	604800
    @	IN	SOA	x51rl.example.org.ua. root.x51rl.example.org.ua. (
    			      2		; Serial
    			 604800		; Refresh
    			  86400		; Retry
    			2419200		; Expire
    			 604800 )	; Negative Cache TTL
    ;
    @	IN	NS	x51rl.example.org.ua.
    @	IN	A	127.0.0.1
    @	IN	AAAA	::1
    *.x51rl.example.org.ua. IN A 127.0.0.1</code>


Now when all done, u may check that all is working with:

    
    <code>named-checkzone x51rl.example.org.ua /etc/bind/db.x51rl.example.org.ua</code>


And if all ok, restart bind:

    
    <code>sudo /etc/init.d/bind9 restart</code>


Now this command must work:

    
    <code>nslookup aaa.bbb.x51rl.mam.org.ua 127.0.0.1</code>


If u using dhcp in your network, it will owervrite your resolv.conf, so edit:

    
    <code>sudo nano /etc/dhcp3/dhclient.conf</code>


and uncomment line

    
    <code>prepend domain-name-servers 127.0.0.1;</code>


now after restarting networking u will be able to ping any subdomain.


All that left is to configure Apache Mass Virtual Hosting


http://serverfault.com/questions/112535/apache2-virtualhost-auto-subdomain

http://httpd.apache.org/docs/2.2/vhosts/mass.html

