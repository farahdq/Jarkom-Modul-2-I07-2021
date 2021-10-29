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

![image](https://user-images.githubusercontent.com/73812417/139353769-bf8f26cf-a2d5-4f88-9c83-9cf8c35e14d7.png)

Then, don't forget to configure the network for each of them

![image](https://user-images.githubusercontent.com/73812417/139353876-951e27fb-74b3-4abc-9c7d-e6ad8577a835.png)
Network Configure Foosha

![image](https://user-images.githubusercontent.com/73812417/139353917-f6015e72-9c1e-4412-b882-a70e7bd57069.png)
Network Configure Loguetown

![image](https://user-images.githubusercontent.com/73812417/139353952-e0088e47-a500-46ad-bd81-868ffb0a420c.png)
Network Configure Alabasta

![image](https://user-images.githubusercontent.com/73812417/139353985-794ce289-9fdd-45b0-bf10-bb7c4312c9b3.png)
Network Configure Ennieslobby

![image](https://user-images.githubusercontent.com/73812417/139354028-5546654b-5825-49c6-9e70-b2d14e12ff76.png)
Network Configure Water7

![image](https://user-images.githubusercontent.com/73812417/139354047-777ee2ba-d2f9-40ef-9d8c-04a49d232f02.png)
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

![Screenshot (814)](https://user-images.githubusercontent.com/73812417/139094621-9a020cf5-949b-4f43-827c-1668c54d56ea.png)

Then add the zone to `/etc/bind/named.conf.local` by adding:
<pre>
    zone "super.franky.i07.com" {
            type master;
            file "/etc/bind/kaizoku/super.franky.i07.com";
    };
</pre>

![Screenshot (815)](https://user-images.githubusercontent.com/73812417/139094776-833dcae7-c348-4a1d-9030-290da92c4b3a.png)


Then copy `/etc/bind/db.local` to `/etc/bind/kaizoku/super.franky.i07.com`\
Then configure the file to have a SOA `super.franky.i07.com.`, NS `super.franky.i07.com.`, record A which leads to `IP Skypie`, and CNAME `www` at `super.franky.i07.com.`
![3 3](https://user-images.githubusercontent.com/73812417/139095496-afcf269f-e2a1-492f-b9a2-fefd66cd82ea.jpeg)

## No 4

Also create a reverse domain for the main domain

First, add a zone to `/etc/bind/named.conf.local` by adding:
<pre>
    zone "2.41.10.in-addr.arpa" {
            type master;
            file "/etc/bind/kaizoku/2.41.10.in-addr.arpa";
    };
</pre>

![4 1](https://user-images.githubusercontent.com/73812417/139095885-07719498-563d-4f2c-b3fe-e77c11c0db88.jpeg)


Then copy `/etc/bind/db.local` to `/etc/bind/kaizoku/2.41.10.in-addr.arpa`\ 
Then configure the file to have SOA `franky.i07.com.`, `2.41.10.in-addr.arpa.` which has NS `franky.i07.com.`, and `2` which is the 4th byte of ENIESLobby IP has PTR `franky.i07.com.`


![4 2](https://user-images.githubusercontent.com/73812417/139096062-615483d3-ea1f-48e4-9bc7-9b0709ae2238.jpeg)

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

![5 1](https://user-images.githubusercontent.com/73812417/139096229-aa19f4a5-6ff8-41bc-99c5-f79b126fb8fd.jpeg)

On Water7, run the `apt-get update` and `apt-get install bind9 -y` command to install bind9

Then edit '/etc/bind/named.conf.local' by adding:
<pre>
    zone "franky.i07.com" {
        type slave;
        masters { 10.41.2.2; };
        file "/var/lib/bind/franky.i07.com";
    };
</pre>

![5 2](https://user-images.githubusercontent.com/73812417/139096445-b02c5d63-7c01-4db7-9d93-4d46bec8b051.jpeg)


## No 6

After that there is a subdomain mecha.franky.yyy.com with the alias www.mecha.franky.yyy.com delegated from EniesLobby to Water7 with IP to Skypie in sunnygo folder.

On EniesLobby, edit the file `/etc/bind/kaizoku/franky.i07.com` by adding:
<pre>
        ns2     IN  A   10.41.2.3; IP Water7
        mecha   IN  NS  ns2
</pre>

![6 1](https://user-images.githubusercontent.com/73812417/139096597-2a6cf7f0-ff3f-4a66-8598-d43b46eb07da.jpeg)

Then edit the file `/etc/bind/named.conf.options` with the comment section `dnssec-validation auto;` and add the line `allow-query{any;};`

![6 2](https://user-images.githubusercontent.com/73812417/139096746-9a9d4cba-94f9-466f-99e0-ea7a432c37e3.jpeg)

Make sure there is already a line `allow-transfer { "IP Water7"; };`  in the zone `franky.i07om` in the file `/etc/bind/named.conf.local`

On Water7, edit the file `/etc/bind/named.conf.options` with the comment section `dnssec-validation auto;` and add the line `allow-query{any;};`

![6 3](https://user-images.githubusercontent.com/73812417/139097714-f4305bc3-44b8-4891-b4d5-d3bff9dc1dfa.jpeg)

Then add the zone to `/etc/bind/named.conf.local` by adding:
<pre>
    zone "mecha.franky.i07.com"}
            type master;
            file "/etc/bind/sunnygo/mecha.franky.i07.com";
    };
</pre>

![6 4](https://user-images.githubusercontent.com/73812417/139097809-a10516de-0587-4f58-a47b-9a4348a80161.jpeg)

Then created sunnygo folder in `/etc/bind` Then copy `/etc/bind/db.local` to `/etc/bind/sunnygo/mecha.franky.i07.com`\
Then configure the file to have a SOA `mecha.franky.i07.com.`, NS `mecha.franky.i07.com.`, record A leading to `IP Skypie`, and CNAME `www` on `mecha.franky.i07om.`

![6 5](https://user-images.githubusercontent.com/73812417/139097968-b5caf0c6-5119-4eeb-b40e-9df44507e3b0.jpeg)


## No 7

To facilitate the communication of Luffy and his partner, a subdomain was created through Franky under the name general.mecha.franky.yyy.com with the alias www.general.mecha.franky.yyy.com leading to Skypie.

On Water7, edit the file `/etc/bind/sunnygo/mecha.franky.i07.com` by adding:
<pre>
        ns1     IN  A   192.194.2.4 ; IP Skypie
        general IN  NS  ns1
</pre>

![7 1](https://user-images.githubusercontent.com/73812417/139098109-dd0ca4a1-0183-4732-a604-a0df9f2e4b18.jpeg)

Then add the zone to `/etc/bind/named.conf.local` by adding:
<pre>
    zone "general.mecha.franky.i07.com" {
            type master;
            file "/etc/bind/sunnygo/general.mecha.franky.i07.com";
    };
</pre>

![7 2](https://user-images.githubusercontent.com/73812417/139098219-881351aa-46e8-4f18-bbe2-6653a6456d49.jpeg)

Then copy `/etc/bind/db.local` to `/etc/bind/sunnygo/general.mecha.franky.i07.com` 
Then configure the file to have SOA `general.mecha.franky.i07.com.`', NS `general.mecha.franky.i07.com.`, record A which leads to `IP Skypie`, and CNAME `www` on `general.mecha.franky.i07.com.`

![7 3](https://user-images.githubusercontent.com/73812417/139098371-c7b2448b-0c53-43ed-ad19-c244e7e766f0.jpeg)


## No 8

After configuring the server, the Webserver configuration is carried out. First with the webserver www.franky.yyy.com. First, luffy needs a webserver with DocumentRoot at /var/www/franky.yyy.com.

On Skypie, run command `apt-get update` then, install apache2 using command `apt-get install apache2 -y`, and do it also for installing php using command `apt-get install php -y` and lastly `apt-get install libapache2-mod-php7.0 -y`

*SS nya*

Then do 'wget' to download the required file. After that unzip it and remove the trash using the command :

`wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/franky.zip` \
`wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/general.mecha.franky.zip` \
`wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip` 

`unzip franky.zip` \
`rm -r franky.zip` 

`unzip general.mecha.franky.zip` \
`rm -r general.mecha.franky.zip` 

`unzip super.franky.zip` \
`rm -r super.franky.zip` 

*SS nya*

After that, move to the directory `/etc/apache2/sites-available` Then copy the `000-default.conf` file into the file `franky.i07.com.conf`

*SS nya*

Then set the file `franky.i07.com.conf` to have the lines `ServerName franky.i07.com`, `ServerAlias www.franky.i07.com`, and `DocumentRoot /var/www/franky.i07.com`

![8](https://user-images.githubusercontent.com/73812417/139098705-662c6b54-4da9-421b-94e2-a8f89a3c6633.jpeg)

Then create a new directory with the name `franky.i07.com` on `/var/www/` using the command `mkdir/var/www/franky.i07.com` 
Then copy the contents of the 'franky' folder that has been downloaded to `/var/www/franky.i07.com`

*SS nya*

After that run the command `a2ensite franky.i07.com` and `apache2 service restart`

*SS nya*

## No 9

After that, Luffy also needs that the url www.franky.yyy.com/index.php/home can become a www.franky.yyy.com/home.

First, run the command `a2enmod rewrite` then `apache2 restart service`

Then move to the directory `/var/www/franky.i07.com` and create a `.htaccess` file with the contents of the file:
<pre>
    RewriteEngine On
    RewriteRule ^home$ index.php/home
</pre>

![9 1](https://user-images.githubusercontent.com/73812417/139098858-a8c0d281-e48f-441f-9883-88f551727edd.jpeg)

Then open the file `/etc/apache2/sites-available/franky.i07.com.conf` and add:
<pre>
```
    <Directory/var/www/franky.i07.com>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
```
</pre>

![9 2](https://user-images.githubusercontent.com/73812417/139099019-fb2c2cf2-48ed-4220-8d40-409f80367c19.jpeg)


## No 10

After that, on the subdomain of www.super.franky.yyy.com, Luffy needs to store assets that have DocumentRoot at /var/www/super.franky.yyy.com

First, move to the `/etc/apache2/sites-available` directory. Then copy the `000-default.conf` file into the file `super.franky.i07.com.conf`

Then set the file `super.franky.i07.com.conf` to have the lines `ServerName super.franky.i07.com`, `ServerAlias www.super.franky.i07.com`, and `DocumentRoot /var/www/super.franky.i07.com`

![10 1](https://user-images.githubusercontent.com/73812417/139099270-37e38c12-72f3-4784-a915-69dfcae06107.jpeg)

Then create a new directory with the name `super.franky.i07.com` on `/var/www/` using the command `mkdir/var/www/super.franky.i07.com`
Then copy the contents of the 'super.franky' folder that has been downloaded to `/var/www/super.franky.i07.com`

After that run the command `a2ensite super.franky.i07.com` and `apache2 service restart`


## No 11

However, in the /public folder, Luffy wants to only be able to directory listings only.

Move to the `/etc/apache2/sites-available` directory then open the `super.franky.i07.com` file and add:
<pre>
```
    <Directory/var/www/super.franky.i07.com/public>
        Options +Indexes
    </Directory>
```
</pre>

![11 1](https://user-images.githubusercontent.com/73812417/139099453-32632c89-dedd-4a8e-b03e-7f3a1c21f1e6.jpeg)


## No 12

Not only that, Luffy also set up a 404.html error file in the /errors folder to replace the code error on apache.

Move to the `/etc/apache2/sites-available` directory then open the `super.franky.i07.com` file and add:
<pre>
        ErrorDocument 404/error/404.html
</pre>

![12 1](https://user-images.githubusercontent.com/73812417/139099597-8c9827c1-4b21-41cf-9edd-77393cc24930.jpeg)


## No 13

Luffy also asked Nami to create a virtual host configuration. This virtual host aims to be able to access asset files www.super.franky.yyy.com/public/js becomes a www.super.franky.yyy.com/js.

Move to the `/etc/apache2/sites-available` directory then open the `super.franky.i07.com` file and add:
<pre>
        Alias "/js" "/var/www/super.franky.i07.com/public/js"
</pre>

![13 1](https://user-images.githubusercontent.com/73812417/139099726-20ee2a3f-1e5d-4f35-aa04-7b2f2e4adb5f.jpeg)


## No 14

And Luffy asked for web www.general.mecha.franky.yyy.com can only be accessed with port 15000 and port 15500.

First, move to the `/etc/apache2/sites-available` directory. Then copy the `000-default.conf` file into the file `general.mecha.franky.i07.com.conf`

Then set the file `general.mecha.franky.i07.com.conf` to have `<VirtualHost *:15000 *:15500>`, line `ServerName general.mecha.franky.i07.com`, `ServerAlias www.general.mecha.franky.i07.com`, and `DocumentRoot /var/www/general.mecha.franky.i07.com`

![14 2](https://user-images.githubusercontent.com/73812417/139100051-fa200183-132e-4ad2-bcef-6aea4700c4df.jpeg)

Then add ports 15000 and 15500 to the `/etc/apache2/ports.conf` file by adding:
<pre>
    Listen 15000
    Listen 15500
</pre>

![14 3](https://user-images.githubusercontent.com/73812417/139100295-0fbf13fb-6c56-4b99-8e52-94caf1839949.jpeg)

Then create a new directory with the name `general.mecha.franky.i07.com` on `/var/www/` using the command `mkdir/var/www/general.mecha.franky.i07.com` 
Then copy the contents of the 'general.mecha.franky' folder that has been downloaded to `/var/www/general.mecha.franky.i07.com`

After that run the command `a2ensite general.mecha.franky.i07.com` and `apache2 service restart`


## No 15

with the authentication of luffy username and onepiece password and file at /var/www/general.mecha.franky.yyy

First, run the command `htpasswd -c /etc/apache2/.htpasswd luffy` to create a file that stores username and password into the file `/etc/apache2/.htpasswd` with user `luffy`, then there will be a prompt to enter and confirm the password.

Then, edit the file '`/etc/apache2/sites-available/general.mecha.franky.i07.com.conf`' by adding:
<pre>
```
    <Directory/var/www/general.mecha.franky.i07.com>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
```
</pre>

![15 1](https://user-images.githubusercontent.com/73812417/139101473-b64d6dc7-7ce2-4100-89a1-72cf09a11efc.jpeg)

Then, edit the file '`/var/www/general.mecha.franky.i07.com/.htaccess`' to:
<pre>
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</pre>

![15 2](https://user-images.githubusercontent.com/73812417/139100716-a9100ed4-5e20-479a-a6e5-5aaf92780003.jpeg)


## No 16

And every time you access an EniesLobby IP, it will be automatically accessed to www.franky.yyy.com

Because we can't access EniesLobby's IP, the IP we switched to is when accessing Skypie IP. Then, edit the `/var/www/html/.htaccess` file by adding:
<pre>
    RewriteEngine On
    RewriteBase /
    RewriteCond %{HTTP_HOST} ^10\.41\.2\.4$
    RewriteRule ^(.*)$ http://www.franky.i07.com [L,R=301]
</pre>

![16 1](https://user-images.githubusercontent.com/73812417/139100824-78eb8112-0052-469b-97db-99fd53c2451f.jpeg)

Then, edit the file `/etc/apache2/sites-available/000-default.conf` by adding:
<pre>
```
    <Directory/var/www/html>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
```
</pre>

![16 2](https://user-images.githubusercontent.com/73812417/139100921-2d12f6e9-75b5-4876-a860-103439e5b528.jpeg)


## No 17

Because Franky also wants to invite his friend to be able to contact him through the website www.super.franky.yyy.com, and because web server visitors will definitely be confused by the random images, Franky also asks to replace the request of images that have substring "franky" will be directed towards Franky.png.

First, edit the file `/etc/apache2/sites-available/super.franky.i07.com.conf` by adding:
<pre>
```
    <Directory/var/www/super.franky.i07.com>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
```
</pre>

![17 1](https://user-images.githubusercontent.com/73812417/139101046-d11b6413-9e22-46c8-a97d-b9cb917aa485.jpeg)

Then, edit the file `/var/www/super.franky.i07.com/.htaccess` by adding:
<pre>
    RewriteEngine On
    RewriteRule ^(.*)franky(.*)\. (jpg|gif|png)$ http://super.franky.i07.com/public/images/franky.png [L,R]
</pre>

![17 2](https://user-images.githubusercontent.com/73812417/139101244-7f9bf941-5af6-49f1-8503-7c966e013e02.jpeg)



