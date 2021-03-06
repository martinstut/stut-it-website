---
layout: post
title: Privacy-Respecting Browser Add-Ons
date: 2017-05-02 17:11:00 +02:00
type: post
published: true
status: publish
categories:
- test
tags:
- mitmproxy
- privacy
- browser
- addon
- tracking
meta:
author: Martin Stut
---
There are credible claims, outlined in [this blog post](https://www.stut-it.net/blog/2017/keep-your-web-server-from-broadcasting-your-intranets-structure.html),  that many browser add-ons are sending your full browsing history to analytics companies that may sell this information to anyone willing to pay a certain sum of money. To be sure which browser add-ons are benign, I have used the technique described in [this blog post](https://www.stut-it.net/blog/2017/watching-windows-internet-traffic-mitmproxy.html) to analyze the traffic generated by certain popular browser add-ons and similar products. The results seem to indicate that the more tracking protection a product claims, the more risk there is that it replaces external tracking with its own tracking.

To keep this article short, I'll only summarize the results, writing only brief details about the findings. If there is public interest in how I came to the conclusion about one or the other product, please write to me and I may write a public or private summary why I think product X might be doing things users might not want.

## Whitelist

Extensions in this list look clean. I did not find suspicious traffic. Of course this doesn't mean there is no unwanted traffic. I may just have failed to spot the issues...

* AdBlock Plus
* Certificate Patrol
* Classic Theme Restorer
* DisableBackspaceNavigation
* Disconnect for Facebook
* Down Them All
* DuckDuckGo Plus
* Easy Screenshot (by tyzbit, not the "Web-Clipper: Easy Screenshot" variant.)
* Exif Viewer (Chrome: the version of Rodrigue)
* FoxyProxy Standard (semi-greylist reason: every click on a link in the options page triggers a google-analytics request)
* FireFTP
* Google Disconnect
* KeeFox (does send anonymous statistics, but that is really anonymous, so it's o.k.)
* LastPass
* Nagios Checker
* NoScript
* Perspectives (does need to send browsing information to notaries to do its job: full domain name and port number of every site visited. No cookies, no tracking IDs, so my impression is they did the job right.)
* Privacy Badger
* SelfDestructing Cookies
* Twitter Disconnect
* uBlock Origin
* Video Download Helper

## Blacklist

Extensions in this list clearly send encrypted traffic beyond what is necessary for legitimate operation, or send tracking IDs (by cookie or request data) despite claiming to protect from tracking. To me, encrypted traffic within an already HTTPs encrypted connection indicates the desire to hide something. Hiding content is o.k. for password managers or chat systems, but not for products that should not send anything first place.

Do not use these products if you are visiting confidential members-only websites or if for other reasons would mind your browsing history ending up in the hands of ad-optimizers or anyone else willing to pay for web analytics of other sites.

### Avira Browser Plugin

* many encrypted POSTs to signer-proxy-network.cliqz.com
* huge encrypted POSTs to http://54.234.127.117/verify
* a few GETs sending encrypted data to https://ame.avira.com/ame/redirect?data
* a few large POSTs of encrypted data to https://api.mixpanel.com/track/ .

### Avira Scout Browser
* connections to doubleclick, google-analytics, cliqz and other sites
* entering an address like heise.de sends this address to cliqz, as cliqz is the default search engine
* gets png files from search.avira.net that have long hex filenames that could well serve as tracking IDs; these are linked to mixpanel cookies
* POST requests to  https://signer-proxy-network.cliqz.com/register containing a long (ca. 200 characters) encrypted "pk" value
* sends keystroke values to logging.cliqz.com

### Cliqz Browser

* claims to do anti-tracking, but adds a lot of tracking on its own to reports.cliqz.com/submit/telemetry
* sends lots of JSON data that looks like non-privacy-invading telemetry (machine properties, list of add-ons, ...), but including a client ID
* several requests to hpn-sign.cliqz.com/sign: ca. 2000 bytes of encrypted JSON fields
* several requests to 35.157.31.154/verify (HTTP, not even HTTPS, IP-numbers vary): ca. 23 kBytes of encrypted JSON fields
* several requests to stats.cliqz.com looking similar to telemetry, but including a session ID

### Ghostery

The "Welcome to Ghostery" page sent tracking requests to analytics.cliqz.com, retrieving a piwik script and then sending a tracking message.

IMO promising to stop trackers but at the same time tracking users is not fair play.


## Greylist

Extensions in this list are doing things that look suspicious. They may be benign, but this is hard to tell. I do not recommend using these products with sensitive data, such as health, banking or confidential Intranet sites.

### Blur

During surfing, apparently all forms encountered (field names, no values) are sent to mapping.abine.com . This submission includes the domain. So they seem to be mapping innocent web sites for form metadata.

Given the amount of trust needed for a password store, there is too much encrypted traffic compared to the purpose.

### Weather Underground

No highly suspicious traffic, although significant amounts of cookies, including optimizely.com, in the weather data requests. Calls to pixel.rubiconproject.com including ruid cookies, which probably enable user tracking.

## Browser Safety Systems

Most browsers make use of one of these systems to help protecting you from social engineering attacks and malicious downloads. Each system makes the browser send browsing information to a major pool, which may not be what you want, privacy-wise. You need to make a judgment whether you want to give up some privacy in return for some protection against malicious websites.

### Google SafeBrowsing

Turned on by default in Mozilla Firefox.

> ... works by checking the sites that you visit against lists of reported phishing, unwanted software and malware sites.
>
> ...
>
> When you download an application file, Firefox checks the site hosting it against a list of sites known to contain "malware". If the site is found on that list, Firefox blocks the file immediately, otherwise it asks Google’s Safe Browsing service if the software is safe by sending it some of the download’s metadata ... ... including the **name, origin, size and a cryptographic hash of the contents**.

This means, essentially all downloaded executable files are reported to Google. If you don't want that, and don't need the protection, turn it off by going to Firefox > Preferences > Security > "Block dangerous and deceptive content" . Do work from bottom to top, as otherwise some boxes stay ticked but greyed out.

### Windows SmartScreen

According to [Wikipedia](https://en.wikipedia.org/wiki/Microsoft_SmartScreen), every website and download is checked against a local list of popular legitimate websites; if the site is not listed, the entire address is sent to Microsoft for further checks. So all visited URLs of confidential Intranet sites, certainly  not in the list of popular legitimate websites, will get sent to Microsoft.

You are paying this privacy price in exchange for a high rate (95-99% according to Wikipedia) of protection against social engineered malicious websites. So you need to "choose your poison".

