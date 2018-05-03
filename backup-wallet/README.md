# How to back up your zaap wallet

## Introduction

This guide will explain how to back up your wallet and how to recover your wallet from a backup copy.

It is important to know that zaap Core saves the data of your wallet in a file called `wallet.dat`.

If you lose access to this file, you will permanently lose access to your wallet. So it is recommended to make backups outside the current device.

## zaap Wallet Location

Your `wallet.dat` file should be in following path depending on Operating System (OS):

* Windows: `%APPDATA%\zaapCore`.
* Linux: `~/.zaapcore`.
* macOS: `~/Library/Application Support/zaapCore`

## Encrypt zaap wallet

Before starting, I recommend that you encrypt your wallet. With this, you will add more security to your wallet since to access it you will need a password. And, please, do not keep the password and the wallet.dat file in the same place (especially if you save it in the cloud).

To encrypt your zaap Wallet, you have to go to the "Settings" tab and select "Encrypt Wallet...".

## Create a zaap wallet Backup

While zaap Core is running do not copy the wallet.dat file because it can create data corruption problems. It is advisable to use the following procedure.

To create a backup using zaap Core you need to click on the "File" tab and then click on "Backup Wallet". Now you have to select where are you going to save your file.

## Restore a zaap wallet Backup

In order to restore a backup of your wallet, you must download the latest version of zaap Core. If you already have zaap Core installed, make sure it is not running.

Now you have to go to the path where the zaap Core wallet is and rename existing `wallet.dat` file to `wallet_old.dat`.

To finish you will have to copy the backup copy of your wallet to the zaap Core route and name it `wallet.dat`.
