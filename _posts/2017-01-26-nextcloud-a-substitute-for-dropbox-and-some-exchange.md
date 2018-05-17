---
layout: post
title: NextCloud - a Substitute for Dropbox and some Exchange
date: 2017-01-26 19:45:00.000000000 +01:00
type: post
published: true
status: publish
categories:
- test
meta:
author:
  display_name: Martin Stut
---
[NextCloud](https://www.nextcloud.com) is a server application, primarily providing a file synchronisation service, very similar to Dropbox. Calendars, tasks, contacts and a number of other services are available as add-ons. It can, and is meant to, be hosted on **your** servers, possibly on-premise, thus eliminating the privacy issues associated with external cloud providers.   NextCloud is open source. **You can install and use NextCloud free of charge for any purpose**, personal and commercial.  
The server software is complemented by client software for the file synchronization service, free for Linux (todo try Linux client), Mac, Windows  Android, small fee  for iOS.  
For calendar, tasks and contacts there are many clients, most notably [Outlook CalDAV Synchronizer](http://caldavsynchronizer.org/) on Windows and Lightning for Thunderbird (calendars and limited tasks, Linux, Mac, Windows). iOS can connect natively, Android needs a paid app (CalDAV Sync and CardDAV Sync by Marten Gajda, 2.89 € and 1.79 € respectively).

NextCloud GmbH get its revenue from support contracts, starting at 1900 Euros per year, thus being only relevant for installations approaching or exeeding 50 users. Documentation on their web site is complete and concise enough to keep me from taking notes how to set up NextCloud.


## Technology (LAMP)

The NextCloud server is essentially a set of PHP scripts that integrates into a web server (Apache preferred) running on Linux. It provides a web GUI for human users, a WebDAV interface to files, a CalDAV interface to calendars and tasks and a CardDAV interface to contact lists a.k.a. address books.

For the database backend you essentially have a choice of SQLite and MySQL (PostgreSQL is supported too, Oracle is only available for paying support contract holders). For anything larger than a single user or testing instance, MySQL is highly recommended.

Of course you need an adequate amount of file storage for all the data your users will be storing,  including previous versions and deleted files. During setup, you can chose the storage path. Multiple storage backends other than a file system path are supported by "external storage" functionality. I have not tried much with these.

Installation and updates work as documented in the [Admin manual](https://docs.nextcloud.com/server/11/admin_manual/), so I won't repeat that here. The built in updater works quite well, if you loosen permissions for updating and tighten them after updating. For details see the [Upgrading Nextcloud with the Nextcloud App](https://docs.nextcloud.com/server/11/admin_manual/maintenance/update.html) section of the  [Admin manual](https://docs.nextcloud.com/server/11/admin_manual/).

## Functionality

### File Storage and Synchronization

This is the core of NextCloud's capability. Every user has his own root folder which usually contains many folders with subfolders etc. It is recommended to store documents in folders or subfolders, not on the root level.

Each folder has one user as the owner. Beware: If the owner account is deleted, e.g. when the account holder leaves the organization, all files and folders inside this folder are deleted too. This means that it is very useful to create important shared folders e.g. as admin or as a special folder holding account, to avoid unwanted deletion when some key user leaves the organization.

#### Client Software

Client file synchronization software synchronizes selected server folders with a folder on your client computer, so you have your server files always available on your computer for using them at local disk speed, even when your Internet connection is not active - think Dropbox. The desktop client automatically synchronizes all changes in both directions as soon as connectivity returns. If conflicts arise, typically because another user has modified the same file during the same time you did, both versions are kept, but one of them gets a conflict label added to the file name, similar to Dropbox.

The desktop client is available for all major platforms:

* Mac OS (free)
* Windows (free)
* Linux (free)

Mobile clients are avaible for

* Android (free)
* iOS (still in beta stage, 0.99 US$)

The Android client needs quite a bit of manual nudging to re-synchronize files. Maybe my use case was not anticipated by the developers: keeping a handfull of text files on the smartphone, editing them locally (with any plain text editor, my favourite is Jota Editor). If there is connectivity, in most cases the Android NextCloud client sends the file to the server immediately after I click "save". But when there is no connectivity (I'm frequently behind a guest network that requires captive portal registration every two hours), or when I want to check for server-side changes, I need to enter the text file's directory in the NextCloud client to manually trigger synchronisation. The Android NextCloud client does have a "synchronize now" button, but when I click it, it takes 45 minutes (sic) to complete a sync run. I don't want to wait that long to transfer my desktop edits to the smartphone.

All client versions have multi-account capability, which is essential e.g. if you have accounts on your home NextCloud, an office NextCloud, a church nextcloud etc. Dropbox doesn't need this, because there is only one Dropbox server. NextCloud needs multi account capability, because anyone can host his own NextCloud server.

#### Permission logic

A user can share each folder to multiple other users and/or groups. Each share, different for each user or group, can have zero or more of these permission bits set:

* read-only
* edit, separated into
  * create
  * modify
  * delete
* can share

Setting no bit (checkmark) is equivalent to read-only sharing.

Important: The users, both sharing and receiving, of a share will see the shared folder in the root of their own directory, even if it originally was in some subfolder of the sharing user. Thus it is recommended to share only root directory folders.

#### External Sharing by Link

Each file or folder, no matter how deep inside the folder hierarchy, can be shared by link, optionally with

* read-write or read-only permission
* password 
* expiration date.

This link can be sent to anyone, especially if they don't have an account on this NextCloud.

Limitation: each file or folder can have only one (1) sharing link. If you happen to need different levels of access, consider giving an account to those who need higher levels of access, e.g. write permission.

#### Online Editing

You can edit plain text files within the web interface, which can be useful if you want to modify a piece of text while away from a cloud-synced computer, e.g if using your smartphone feels too clumsy for the amount of editing you are planning but you have a desktop browser available (I often am in this situation during customer visits).

The optional Documents app, which needs to be enabled by an administrator, can perform online editing of rich text and save it in OpenDocument .odt format, known from OpenOffice/LibreOffice.

### Calendar and Tasks

To use calendar and tasks, an administrator needs to enable the calendar and optionally tasks apps. This is only a few mouse clicks. 

Every user has a standard personal calendar and can create more calendars. Each calendar can be shared to other users and/or groups with or without write permission. That's it. Simple but still powerful. A lot easier than other groupware systems I have managed.

Each calendar is being assigned one colour from a choice of 8 predefined colours. If you receive a calendar share, you'll see that calendar in the same colour the owner has set for it. There is no way to change the colour other than asking the owner to do it. In the web interface, all calendars are merged into a single view, where the colours are the only way to quickly see which calendar an appointment is stored in. But even in slightly larger organizations, well exceeding 8  calendars/colours, you can practically use the web interface, because in NextCloud (in contrast to OwnCloud) you can turn off and on individual calendars.

But the web interface is not often used, because the calendars are accessible through the standard CalDAV protocol, essentially specially formatted content inside HTTP(S). Any client programs that speak a sufficiently similar dialect of CalDAV can synchronize with NextCloud.

The classic client on the desktop is Lightning, part of the widely used email client Thunderbird, available for Linux, Mac and Windows.

A cool new (to me) client is [Outlook CalDAV Synchronizer](http://caldavsynchronizer.org/). It installs a plugin to Outlook that is relatively easy to set up and synchronizes calendars, tasks and contacts. NextCloud is in their list of fully supported server types. To me this is one of the more important discoveries of software in years, because it opens a **working** path to group calendaring for Outlook with a much smaller footprint than a full blown Microsoft Exchange server and without the privacy issues of Microsoft Office 365.

On Android, you need a paid app. I have tried several of them. Many do not not work reliably, but CalDAV Sync by Marten Gajda (2.69 € on Google Play) is one of the few that really work. Stay away from free synchronizers as they don't support all relevant fields or limit the number of appointments to synchronize, which can be painful if the excess gets wiped from your server.

iOS can connect natively.

If the server is set up correctly (two lines in the root level .htaccess), you can set up the mobile clients by just entering cloudserver.example.com, without needing to precisely type suffixes like 
/nextcloud/remote.php/dav/calendars/joe/personal


#### Tasks

The NextCloud tasks app adds a task list to each calendar. There are many attributes to a task, even subtasks, but only some attributes get synchronized via CalDAV Sync, largely depending on the client used. I had tasks with a start date and a due date appear as multi-day calendar entries - it does look odd when you get a multi-day appointment for a little task that doesn't matter if you do it on Monday, Tuesday or Wednesday. So you might want to restrict your use of the tasks functionality (e.g. "never use start dates") or set aside a separate calendar for those tasks.

The web interface supports all features, while each client supports a different subset. You can use the same clients as for calendars.


### Contacts

NextCloud can manage contact lists, a.k.a. address books, and cater for client programs using the CardDAV protocol. I do have repeated success with these client programs:

* Thunderbird using the [Inverse SOGo Connector](https://sogo.nu/download.html#/frontends)
* Android, using CardDAV Sync by Marten Gajda (paid app, 1.79 € on Google Play, don't waste your time trying a free version)
* Outlook on Windows using [Outlook CalDAV Synchronizer](http://caldavsynchronizer.org/)
* The web interface built into NextCloud. Really good for editing.


## Summary

All in all, with Nextcloud you get a privacy respecting, self hosted file synchronization service with the additional benefit of simple but reliable calendar, tasks and contact management. I recommend any small to medium organisation to  consider running one when evaluating file synchronization and/or group calendaring servers.
