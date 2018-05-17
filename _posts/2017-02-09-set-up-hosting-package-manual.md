---
layout: post
title: How to Set Up a Hosting Package, the Manual Way
date: 2017-02-09 19:58:00.000000000 +01:00
type: post
published: true
status: publish
categories:
- howto
tags:
- domain
- delegation
- dns
- nameserver
- bind9
- web
- hosting
- apache
- https
- letsencrypt
- email
meta:
author: Martin Stut
---
In this post, I'm describing what it takes to set up a website and a few email addresses, if your usual web hosting company won't do it at a reasonable price. None of these steps are unusual, but it is nice to have them written down in one place, including working examples of the syntax. The target audience are IT technicians, who might end up doing this for several domains.

Usually, at least in the German market, all of this is done by the web hosting company. But recently I had the task to perform this not for a German, but for a Kyrgyz customer. Things do work different there. European hosting companies, if they offer .kg-domains at all, charge 180 to 270 USD per year just for the domain registration, while domain.kg does it for the equivalent of 37 USD per year. So I set out to do it on my own.

## Make Sure You Have At Least Two Name Servers

For reliability, all Internet domain names are required to be resolved by at least two different name servers. Make sure you have access to both of them. If not, get a small VPS (Virtual Private Server) from a provider like [Hetzner](http://www.hetzner.de) or [HostEurope](http://www.hosteurope.de). Details of getting a VPS are beyond the scope of this text.

In several places you need to enter numeric IP-Addresses. In this example I'm using ns2.example.net (9.8.7.6) for the primary (authoritative) name server and ns1.example.net (1.2.3.4) for the secondary name server.

## Get the Domain Delegation

Through an appropriate registrar, domain.kg in this case, "buy the domain". You will need to specify the DNS names and IP addresses of the name servers. In the case I worked with, the customer did this on his own from within Kyrgyzstan.

## Authoritative Name Server

Example IP-Address: 9.8.7.6

The server selected to become the primary name server is a VPS running Debian 7 Linux.

* `apt-get install bind9`
* in /etc/bind/named.conf.local, add a group of lines:

```
zone "example.kg" {
  type master;
  file "/etc/bind/example.kg.hosts";
  also-notify { 1.2.3.4; };
};
```

* in /etc/bind add a file example.kg.hosts, containing this text (for reference, see http://www.zytrax.com/books/dns/ch8/ and https://www.centos.org/docs/5/html/Deployment_Guide-en-US/s1-bind-zone.html):

```
    $ttl 38400
    $origin example.kg.
    @       SOA     ns2.example.net. hostmaster.example.net. (
                    2017020300
                    21600
                    3600
                    604800
                    86400 )
    @       NS      ns2.example.net.
    @       NS      ns1.example.net.
    @       A       9.8.7.6
    www     A       9.8.7.6
```
* Restart bind9 and check /var/log/daemon.log for errors.

## Secondary Name Server

Example IP-Address: 1.2.3.4

* `apt-get install bind9`
* in /etc/bind/named.conf.local, add a group of lines:

```
    zone "example.kg" {
        type slave;
        file "/var/cache/bind/example.kg.hosts";
        masters { 9.8.7.6; };
        };
```
* Restart bind9 and check /var/log/daemon.log for errors.

## Check DNS

The tool of choice on Linux seems to be zonecheck ([man page](https://linux.die.net/man/1/zonecheck)).

1. `apt-get install zonecheck`
2. `zonecheck --ns ns2.example.net\;ns1.example.net example.kg`
   The backslash before the semicolon is important to avoid the shell misinterpreting the semicolon as and of command.
3. Work through zonecheck's errors and warnings.

## Web Server

Assuming apache2 is already installed.

* ```
   adduser example
   cd /home/example
   mkdir public_html
   chown example.www-data public_html
   chmod 755 public_html
   mkdir logs
   chown example.www-data public_html
   chmod 775 logs
  ```

   This enables the webmaster to SFTP (FTP over SSH) into the web server and upload content. No FTP server software needed if you can SSH into the web server.

* Create a dummy index.html web page and upload it to /home/example/public_html by FileZilla or equivalent, using the SFTP (FTP over SSH) protocol. 

* `apt-get install apache2`

* in `/etc/apache2/sites-available`, copy `000-default.conf` to `example.conf` and edit to suit your needs. Important directives include:

```
    ServerName www.example.kg
    ServerAdmin webmaster@example.kg
    DocumentRoot /home/example/public_html
    <Directory /home/example/public_html/ >
               Require all granted
    </Directory>
    ErrorLog /home/example/logs/error.log
    CustomLog /home/example/logs/access.log combined
```

* enable the site: `a2ensite example`

### Arrange for https

Wait until the DNS entry has publicly spread.

Follow the instructions on https://certbot.eff.org/#debianjessie-apache

This creates and enables a new site example-le-ssl.conf in the Apache configuration.

Yes, letsencrypt is really that easy, if you are using a supported OS/web server pair.

## E-Mail

This is the hardest part. I consider it out of reach of a less-than-full-time administrator to manage an SMTP server with reasonable spam filtering.

Options include:

- [Google Apps](https://gsuite.google.com/intl/en/pricing.html) 5 $ per user per month
- https://www.hosteurope.de/E-Mail-Hosting/MailServer/Vergleichen/ , e.g. 20 Euro per month, product name "MailServer Basic", featuring 50 GB of mail storage, up to 3000 mailboxes
- https://www.hosteurope.de/E-Mail-Hosting/Vergleichen/ e.g. single mailbox, up to 100 addresses. 5 Euro setup, 1.50 Euro per month (including 0.50 Euro external domain surcharge), 5 GB storage
- https://www.df.eu/de/e-mail-hosting/ 3 Euro setup, 1.30 Euro per month (including 0.30 Euro external domain surcharge)
  - https://www.df.eu/int/support/df-faq/domains/externe-domains/ seems to indicate, that any qualified (external domain capable) DF web hosting package can be used. It is our choice whether DF services are used for web, mail or both.
  - all packages listed in https://www.df.eu/int/webhosting/ do qualify. There is an external domain surcharge of 0.29 € per month covering the entire package., e.g.
    - Managed Hosting Basic, 4 Euro per month, 25 GB mail space, any number of mailboxes.
    - There is also a WP edition at the same price. Advantage: *they* do the Wordpress updates. And they really do. When I checked a day after the announcement, the update from WordPress 4.7.1 to 4.7.2 had already been applied.
- Microsoft Office 365 Business Essentials. 4.20 Euro per user per month on a yearly basis.
- Microsoft Exchange Online Plan 1: 3.40 Euro plus VAT per user per month
- Mailbox.org: 1 € per month per mailbox. They claim to be very secure, with investigative journalists etc. as a target audience.

In this case we decided to go with df.eu, as about a dozen mailboxes were needed.

## End of the Story

DF's hosting package turned out to be all we need, so I changed all the DNS records to point to df's servers, let the customer change the domain's DNS server to DF's - and considered doing the steps described here as an exercise that won't go into long term production.

