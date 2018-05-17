---
layout: post
title: Step-by-Step Instructions to Get Cryptomator Going (Windows Version)
date: 2017-05-24 08:17:00 +02:00
type: post
published: true
status: publish
categories:
- howto
tags:
- cryptomator
- encryption
- cloud
- instructions
- windows
meta:
author: Martin Stut
---
In my [last post](https://www.stut-it.net/blog/2017/cryptomator-transparent-file-by-file-encryption-overview.html), I described Cryptomator, a free (pay what you want), open-source program that offers transparent, automatic, file-by-file encryption. Now here are detailed step-by-step instructions to get it going.

## 1. Base Installation

1. Visit the [Cryptomator website](https://cryptomator.org) and download `Cryptomator-x.y.z-platform.exe` . `x.y.z` is a placeholder for the version number, 1.2.3 (sic) at the time of this writing (mid-April 2017). `platform` is a placeholder that changes for the 32- vs. 64-bit version. A full example name is Cryptomator-1.2.3-x86.exe for Cryptomator version 1.2.3 for 32-bit Windows. Use the version that matches the bitness of your operating system. If in doubt, use the 32-bit version.
2. Run the file you downloaded by double clicking on it. If it asks whether you want to execute this file, published by "Open Source Developer, Sebastian Stenzel", click on "execute". If it asks whether you want it to perform administrative tasks on your computer, click on "yes".
3. Accept the license agreement (MIT) and click "Next".
4. Accept the suggested Destination Location by clicking Next. Wait for the installation process to complete.
5. Click "Finish", optionally checking the "Launch Cryptomator" box before.

## 2. Set Up Your First Vault

When you start Cryptomator for the first time, or any time you have no vaults set up, it shows you an emtpy list on its left and a line from a text "click here to add new vaults" to a "+" button on its bottom. The "+" button is always there, if you have any vaults defined already. Just the text and the line is visible only when you have no vaults defined in your copy of Cryptomator.

Click this "+" button. A pop down should appear offering you two buttons, so you can select whether you want to **create** a new vault or **open** an existing vault.

### Create a New Vault

1. After clicking on the "+" button, you get a pop-down. Click on the "create vault" line. A "save as" dialogue should appear.
2. In the save-as dialogue, navigate to the parent folder within which you want to create your new vault, for instance `C:\Users\yourname\Dropbox` . In the "file name" line of the save-as dialogue, type the name of the new directory you want to use as a vault, for instance `confidential-stuff` 
3. Click on the save-as dialogue's "save" button. This will create a new directory named `confidential-stuff` under your `C:\Users\yourname\Dropbox` directory and thus could be synchronized by Dropbox. You will be returned to Cryptomator's list of vaults and a password dialogue.
4. Enter the password you want to set for this vault and repeat the password. Then click on "create vault".
   Memorize the password, store it in a password manager or make sure by some other method that you'll remember this vault's password. If you forget the vault's password, the data stored in this vault is as secure from being accessed by you as it is secure from being accessed by strangers, e.g. Dropbox administrators.

### Add an Existing Vault

1. After clicking on the "+" button, you get a pop-down. Click on the "open vault" line. An "open" dialogue should appear.
2. In the open dialogue, navigate to the folder that contains the vault's data, for instance `C:\Users\yourname\Dropbox\confidential-stuff`. You need to locate and select the file named `masterkey.cryptomator` within the vault's directory.
3. Click on the open dialogue's "open" button, or double click the `masterkey.cryptomator` file. This will create a new line in the list of vaults on the left of Cryptomator's main window.

## 3. Open a Vault

1. After starting Cryptomator, you'll see a list of vaults on the left side of Cryptomator's main window. Click on the vault you want to use.
2. On the right (main) side of Cryptomator's main window, click on "unlock vault". You'll see a text entry field "password".
3. Enter the password the creator of the vault (possibly you) has set (and hopefully told you by a secure method) and click on "unlock vault" (hitting the Enter key works too). After some wheel-turning, the right (main) part of Cryptomator's main window turns into a performance graph. Also a file explorer window should open, showing you the decrypted content of the vault (which might be empty when looking into a newly created vault).
4. Before clicking "unlock vault", you may want to click the "more options" button below the password field. The most interesting item you can set is the drive letter. I tend to select X: for the hidden unkown, but it's completely up to you which (still available) drive letter to assign to which vault.

Now you can use the mapped drive, e.g. X:, for your confidential data in the same way as you would use a network drive - which it really is (Cryptomator internally uses the WebDAV protocol to present the decrypted content of a vault). Whenever you save a file residing within the vault, the encrypted version gets updated within a few seconds and thus your cloud storage's synchronization tool can immediately start synchronizing the encrypted version to the cloud.

You can have multiple vaults open at the same time, they just need to have different drive letters.

## 4. Close a Vault

When you have completed your work on the contents of a vault, you can/should close it.

1. Close any programs, such as word processors, that use any of the vault's files.
2. Close any file explorer windows that show any of the vault's directories.
3. If your vault is stored within a cloud synchronized directory, which is the intended use of Cryptomator, wait for cloud synchronization to complete.
4. Open (re-enlarge from the system tray) Cryptomator's main window and select the vault you want to close.
5. In the bottom right of Cryptomator's main window, click on the "lock vault" button. The performance graph should disappear and the password line should be back.

## Differences on Linux

The instructions above are valid for Windows. Linux is a little different:

* For setup, download the .deb (for Debian and relatives, such as Ubuntu or Mint) or .rpm (for RedHat and relatives, such as Cent OS or SuSE) file appropriate for your distribution and bitness of Linux and install it using your distribution's package manager, possibly by double clicking the downloaded file. Details vary widely among distributions.
* The decrypted vault is presented as a WebDAV mount, like `http://localhost:33639/ZLhq_XiwRsGq/name_of_your_vault` . You can change the port number and the name_of_your_vault in Cryptomator's "more options" dialogue.
* The WebDAV mount not being a clean file system path tends to create issues with some applications, especially those that attempt file locking.