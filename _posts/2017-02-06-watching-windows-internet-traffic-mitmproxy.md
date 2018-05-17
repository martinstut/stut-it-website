---
layout: post
title: Watching a Windows Machine's Internet Traffic With Mitmproxy
date: 2017-02-06 18:55:00.000000000 +01:00
type: post
published: true
status: publish
categories:
- general
- howto
tags:
- windows
- https
- mitmproxy
- intercept
meta:
author: Martin Stut
---
I have heard of too many stories of browser add-ons reporting innocent user's browsing history to data vendors, disguised as "anonymous usage statistics", which later turns out to be not very anonymous, but easily traceable to individuals. I have set up an environment to check what really happens. This includes intercepting SSL traffic, which in theory should be hard, but in practice is easy, by using Mitmproxy as a web proxy.

Mitmproxy's home page is [https://mitmproxy.org/](https://mitmproxy.org/) . It is a suite consisting of the interactive program Mitmproxy and the stream dumper (think tcpdump) [mitmdump](http://docs.mitmproxy.org/en/latest/mitmdump.html) . The name is a combination of the acronym Man In The Middle (the type of attack performed here) and proxy, a type of server widely use especially in company's internal networks.

## Installing Mitmproxy

You absolutely need a 64-bit OS to run Mitmproxy on. Attempting it on a 32-bit system fails with odd error messages you won't at first glance relate to 64-vs-32-bit issues.

Mitmproxy is at home on Linux, but there is also a Windows version available. I did it on a desktop PC running Ubuntu Mate 16.04 LTS, 64-bit.

Working along [http://docs.mitmproxy.org/en/latest/install.html](http://docs.mitmproxy.org/en/latest/install.html) :

1. Download `mitmproxy-1.0.2-linux.tar.gz` from [https://github.com/mitmproxy/mitmproxy/releases](https://github.com/mitmproxy/mitmproxy/releases) .
2. Unpack into e.g. /home/martin/bin by `tar xvzf mitmproxy-1.0.2-linux.tar.gz` results in three binaries: `mitmdump`, `mitmproxy` and `mitmweb`.
3. Open the desktop's firewall to permit inbound traffic on TCP port 8080, which is the default port mitmproxy listens on. If you would like some other port than 8080, you can change it by a command line option.
4. `./mitmdump --help` shows a long list of options, I won't repeat here. I found needing only very few of them.

## Intercepting Traffic

1. Run `./mitmproxy` . This is an interactive program (character based, curses-like user interface), initially showing one line per flow (HTTP request-response pair).
2. Configure your browser to use `192.168.127.44:8080` as a proxy for all protocols. In my first test this was Firefox, and it could well have been on a different computer, as long as it can talk TCP on port 8080 to the Mitmproxy machine. 192.168.127.44 happened to be the IP address of the Mitmproxy machine in my home network at the time of testing. 
3. Try visiting an HTTPS website in your browser. This should result in a certificate error - if it doesn't , the certificate checking logic of your browser is broken. At this stage, this is expected behavior, because Mitmproxy has on the fly generated a fake certificate, signed by Mitmproxy's built in mini-CA, which of course is unknown to your browser, at this stage.
4. Install Mitmproxy's CA by visiting http://mitm.it , clicking on "other" and selecting "trust this CA to identify web sites". The precise steps depend on your client operating system and browser version. More on this later.
5. Visit the HTTPS website again. This time it should display in the browser without error message - unless the website has used certificate pinning techniques like HSTS, which security aware websites should do, to make the users detect attacks like this one.
6. To create a file of the recorded traffic, type the W key in the Mitmproxy console window. When asked for a file name, enter e.g. `test.stream`.
7. Quit Mitmproxy by hitting the q key.

## Logging To A File

1. Run  `./mitmproxy -w test2.stream`
2. Visit an HTTPS site, e.g. a NextCloud server. It should display.
3. The terminal windows only lists flow metadata, but test2.stream contains the full content, including the NextCloud user credentials in clear text.
4. When trying to catch the complete behavior of a website or browser add-on, the `--anticache` (modify the request to make the server re-send content possibly cached by the browser) and `--anticomp` (prevent the server from sending compressed data) options can be very relevant, so my recommended command line is `./mitmdump --anticache --anticomp -a log.stream` . `-a` means append instead of `-w` for write/overwrite. I put this line into a little shell script `/home/martin/bin/catch-as-catch-can`, to be able to start catching without having to remember all these options.

## Analyzing Logs

### Filtering

e.g. `./mitmdump -n -r infile -w outfile "~m post"` reads `infile` and creates `outfile`, containing only streams using the `post` method (`~m`) .

For a full list of command line options see [http://docs.mitmproxy.org/en/latest/mitmdump.html](http://docs.mitmproxy.org/en/latest/mitmdump.html)

Filter expressions are described in detail in [http://docs.mitmproxy.org/en/latest/features/filters.html](http://docs.mitmproxy.org/en/latest/features/filters.html)

You can also do filtering interactively by calling `./mitmproxy -n -r infile` and then using keyboard commands:

- Enter goes into a detail view of a flow.
- q always goes back
- ? shows context-dependent help

## Make Windows 10 Communicate Through Mitmproxy

As a testing environment, I'm using a Windows 10 preview VM inside VirtualBox.

In the Windows VM, I have set the proxy to `192.168.127.44:8080`, both in Internet settings and in admin cmd > `netsh winhttp import proxy source=ie`

Installing Mitmproxy's certificate the standard way produced no error message, but both the Edge browser and Windows Update kept complaining about certificate issues. 

What helped was the hint from http://docs.mitmproxy.org/en/stable/certinstall.html , Windows (automated) section: `certutil.exe -importpfx Root mitmproxy-ca-cert.p12` . It asked for a "PFX Password", but Just hitting enter, specifying an empty password, made it go through without error message.

After closing and reopening Edge I could call HTTPS websites which were happily displayed (and logged by Mitmproxy).

When starting Windows Update, Mitmproxy throws certificate verification errors.

Also the Windows 10 telemetry seems to do some non-standard things. Mitmproxy complains

```
   192.168.127.28:54054: CONNECT watson.telemetry.microsoft.com:443
    << HTTP protocol error in client request: Unexpected EOF
```

## Conclusion

After performing the steps above, I have an environment capable of logging and analyzing the data flow of apps and browser add-ons, even if they encrypt network traffic by HTTPS. The observation results of certain products are likely to be the topic of more blog posts.

