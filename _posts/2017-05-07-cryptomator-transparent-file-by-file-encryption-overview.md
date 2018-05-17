---
layout: post
title: Cryptomator - Transparent File-By-File Encryption for Untrusted (Cloud) Storage
date: 2017-05-07 14:07:00 +02:00
type: post
published: true
status: publish
categories:
- howto
tags:
- cryptomator
- encryption
- cloud
- usability
meta:
author: Martin Stut
---
When you want to store confidential data on less trusted (cloud) storage, you need to encrypt it. Container methods, packing many files into one encrypted container file, like VeraCrypt or ZIP, have disadvantages. Cryptomator is a relatively new, free (pay what you want), open-source program that offers transparent, automatic, file-by-file encryption. Read on for more details.

## Rationale

Most likely, you have been looking into using cloud storage to synchronize, share or backup data. But you did not dare to use cloud storage, because your data is confidential and you don't want the cloud storage provider to read it.

Using a VeraCrypt container or an encrypted ZIP file would solve the trust issues, but has disadvantages in handling:

* The mounting/unmounting or unpacking/repacking steps are cumbersome to repeat before and after each editing session.
* A small change in data, like adding a contact or fixing a typo, results in transferring the entire container, which could be Gigabytes.
* In a cloud sharing setting, only one user at a time can open the container for writing. When another user starts opening the container before the first user has finished packing/uploading and the next user has finished downloading, the entire container file will be flagged as a conflicting copy, resulting in lots of manual effort to reconcile the changes.

The solution is an app that encrypts each file individually, transparent to the user.

[Boxcryptor](https://www.boxcryptor.com/) used to be known for this, but around 2012 a cryptographic flaw had been uncovered in the foundation (encFS) of their version 1. There is version 2 now, but it is relatively expensive, especially if you are using it in a business context.

In early 2016, a group of Germans (why so often them?) published **Cryptomator**, a free, open source app, to provide automatic, transparent, client-side file-by-file encryption of entire directories, including files, subdirectories etc.

Cryptomator is available for Windows, Mac and Linux. Beta versions are available for iOS and Android.

## How it works

This is only a quick overview. For details see the [project home page](https://cryptomator.org) and the [documentation](https://cryptomator.freshdesk.com/). Be assured, **using Cryptomator is easy.** So easy, they won a [special prize for Usable Security and Privacy on the CeBIT Innovation Award 2016](http://www.cebitaward.de/preistraeger/finalisten-2016/cryptomator.html) ("joint venture" of the German Federal Ministry of Education and Research and the large annual IT exhibition CeBIT Hannover) .

### One time setup

At first you download and install the app from [cryptomator.org](https://cryptomator.org). Only use this source, as there are malware-ridden fake copies around.

Then you set up a **vault** (or several vaults). A vault is essentially **a directory, usually located inside your cloud app's synchronization space**. Within the app, you select that directory, assign a **password** and an optional drive letter. If someone (maybe you on a different device) is sharing a vault with you, you can't create it (the other guy already did that), but instead "open" it, specifying the directory from your point of view.

**Example** (on Windows): the encrypted data will reside in C:\Users\Yourname\Dropbox\confidential_stuff\ with its decrypted version being visible (and editable) as drive X:

**You can share a vault with others** by sharing access to the (cloud) storage and telling them the password of the vault. There is only one password per vault. Every user is using this same password for this vault. So if you want to share a different set of files with a different group of people, you need to create/use a different vault with a (most likely) different password.

### Daily use

When you want to access a vault's data, you start the **Cryptomator app, select your vault, enter the vault's password and click on "unlock vault"**. Then (obviously only if you got the password right) the **decrypted version gets mapped to drive X:** (or what ever you chose when setting up this vault on your computer) and a file explorer window opens, showing the vault's decrypted content.

Then you can **use drive X: as you would use a network drive**. (Which in fact it is. Cryptomator internally uses WebDAV.)

You can edit, create, delete etc. files and directories to any nesting level. You can e.g. double click a text or graphics file residing within a vault to open it, using your default text or graphics viewer.

**Some applications on some platforms don't like the WebDAV network drive** Cryptomator creates for the decrypted side. One example I frequently encounter is [TaskCoach](http://taskcoach.org/), my favorite self-management system. Version 1.4.3 can open a .tsk file on Cryptomator on Windows, but cannot on Linux. But TaskCoach version 1.4.2 on Linux was able to open a Cryptomator (Linux) stored tsk-file, because of different lock file handling that got "fixed" in 1.4.3.

I have not run into size limitations. One vault I'm using daily has more than 900 files in more than 300 folders, including a local Git repository of a small software development project, totaling more than 5 GB, synchronized to a NextCloud server.

## Use Cases

Whether Cryptomator can be your tool of choice depends on your use case.

Cryptomator is **great** when used for the purpose it was intended: **using a trusted computer to store files on untrusted (cloud) storage**.

But you should **not use Cryptomator** if

* you **can't trust your computer**. Malware could grab your password and/or data before encryption.
* you need to hide the location of sensitive data. If you are hiding something from some guys and those guys start analyzing your computer, they would quickly see the Cryptomator app, open it and see the locations of all your vaults, without having to enter any vault's password. This could lead to the very kind of questions you wanted to avoid by using encryption. You could see this as a variation on the "your computer is not really trustworthy" theme.
* you plan to **edit the files on the cloud server**, such as Google Docs or Microsoft Office 365's on-line editing. Cryptomator encrypts your data on the client, before sending it to the cloud server, with the expressed intent to prevent the server from making sense of it. But remote editing does require the server being able to make sense of the file's content.

## Summary

Cryptomator is a great tool for encrypting your files on lesser trusted storage, as long as you don't mind the locations of encrypted data being well visible. Using Cryptomator is so easy that they won a CeBIT 2016 special prize for Usable Security and Privacy.

