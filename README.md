# Laporan Resmi Modul 2 Jaringan Komputer I07

### Ascarya Arkaandhiyaa Allaam	  (05111942000027)
### Farah Dhiah Qorirah 		       (05111942000018)
### Julius Adetya Eka Bhaswara 	  (05111942000026)
\
First of all, set the topology based on the picture. 
There are :
  1) NAT1 
  2) Foosha as a router
  3) Switch 1 and Switch 2
  4) Loguetown and Alabasta as a Client
  5) EnniesLobby as a DNS Master
  6) Water7 as a DNS Slave
  7) Skypie as a Web Server 

*masukin gambar topologinya*

Then, don't forget to configure the network for each of them

*SS Edit Network Configure Foosha*\
Network Configure Foosha


*SS Edit Network Configure Loguetown*\
Network Configure Loguetown


*SS Edit Network Configure Alabasta*\
Network Configure Alabasta


*SS Edit Network Configure EnniesLobby*\
Network Configure Ennieslobby


*SS Edit Network Configure Water7*\
Network Configure Water7


*SS Edit Network Configure Skypie*\
Network Configure Skypie


## No 1

EniesLobby will be used as DNS Master, Water7 will be used as DNS Slave, and Skypie will be used as a Web Server. There are 2 clients namely Loguetown, and Alabasta. All nodes are connected to the Foosha router, so they can access the internet.

Run command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.41.0.0/16` used to connect to the outside network on the 'Foosha' router\
*SS nya*

Then, add command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.41.0.0/16` to `/root/.bashrc` to run every time the project is started with command `echo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.41.0.0/16" >> /root/.bashrc`\
*SS nya*

After that on all other nodes added the command `echo "nameserver 10.41.122.0"` for setting the DNS IP to `/root/.bashrc` to run every time the project is started with the command `echo echo "nameserver 10.41.122.1" > /etc/resolv.conf >> /root/.bashrc'`\
*SS nya*


## No 2

Luffy wants to contact Franky who is in EniesLobby with denden mushi. You are asked luffy to create a main website by accessing franky.yyy.com with alias www.franky.yyy.com in the kaizoku folder.

On EniesLobby run command `apt-get update` and `apt-get install bind9 -y` to install bind9

Then edit `/etc/bind/named.conf.local` by adding:
<pre>
    zone "franky.i07.com" {
            type master;
            file "/etc/bind/kaizoku/franky.i07.com";
    };\
</pre>
*SS nya*

Then created kaizoku folder in `/etc/bind`. Then copy `/etc/bind/db.local` to `/etc/bind/kaizoku/franky.i07.com`\
Then configure the file to have SOA `franky.i07.com.`, NS `franky.i07.com.`, record A which leads to `IP Skypie`, and CNAME `www` on `franky.i07.com.`\
*SS nya*

## No 3

After that create a subdomain super.franky.yyy.com with the alias www.super.franky.yyy.com that is set up dns in EniesLobby and leads to Skypie.

Edit the file `/etc/bind/kaizoku/franky.i07.com` by adding:
<pre>
        ns1     IN  A   10.41.2.4 ; IP Skypie
        super   IN  NS  ns1
</pre>

*SS nya*

Then add the zone to `/etc/bind/named.conf.local` by adding:
<pre>
    zone "super.franky.i07.com" {
            type master;
            file "/etc/bind/kaizoku/super.franky.i07.com";
    };
</pre>

*SS nya*

Then copy `/etc/bind/db.local` to `/etc/bind/kaizoku/super.franky.i07.com`\
Then configure the file to have a SOA `super.franky.i07.com.`, NS `super.franky.i07.com.`, record A which leads to `IP Skypie`, and CNAME `www` at `super.franky.i07.com.`.

*SS nya*

## No 4

Also create a reverse domain for the main domain

First, add a zone to `/etc/bind/named.conf.local` by adding:
<pre>
    zone "2.41.10.in-addr.arpa" {
            type master;
            file "/etc/bind/kaizoku/2.41.10.in-addr.arpa";
    };
</pre>

*SS nya*

Then copy `/etc/bind/db.local` to `/etc/bind/kaizoku/2.41.10.in-addr.arpa`\ 
Then configure the file to have SOA `franky.i07.com.`, `2.41.10.in-addr.arpa.` which has NS `franky.i07.com.`, and `2` which is the 4th byte of ENIESLobby IP has PTR `franky.i07.com.`

*SS nya*

## No 5

In order to still be able to contact Franky if EniesLobby's server is damaged, then make Water7 as a DNS Slave for the main domain

On EniesLobby, edit the `franky.i07.com` zone on `/etc/bind/named.conf.local` to:
<pre>
    zone "franky.i07.com" {
            type master;
            notify yes;
            also-notify { 10.41.2.3; };
            allow-transfer { 10.41.2.3; };
            file "/etc/bind/kaizoku/franky.i07.com";
    };
</pre>

*SS nya*

On Water7, run the `apt-get update` and `apt-get install bind9 -y` command to install bind9

Then edit '/etc/bind/named.conf.local' by adding:
<pre>
    zone "franky.i07.com" {
        type slave;
        masters { 10.41.2.2; };
        file "/var/lib/bind/franky.i07.com";
    };
</pre>

*SS nya*


























