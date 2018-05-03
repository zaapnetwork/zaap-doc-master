![zaap](https://github.com/zaapnetwork/zaap-doc-master/blob/master/resources/zaap.png "zaap")

# Setup a Masternode with Ubuntu 16.04 VPS

In this guide you will learn how to prepare a zaap Masternode in Ubuntu version 16.04 VPS. Before you start, you need to meet the initial requirements.

This guide was done with the following environment:
* Local: Windows 8.1 64 bit.
* Remote: Ubuntu 16.04 64 bit (fresh Ubuntu server without zaap already installed).
* zaap Version: 1.0.0.

## Initial requirements

### Virtual Private Server (VPS)

A Masternode must be hosted on a [VPS](https://en.wikipedia.org/wiki/Virtual_private_server). Get a VPS from a provider like [DigitalOcean](https://www.digitalocean.com/), [Vultr](https://www.vultr.com/), [Linode](https://www.linode.com/), etc.

The recommended requirements for the master node would be the following:
* Operating system (OS): Linux.
* Linux Distribution: Ubuntu.
* Version: 16.04 (Xenial) 64 bit.
* Memory RAM: At least 1 GB RAM.
* Disk Size: At least 30 GB.

### zaap Transaction

In order to have a zaap masternode you must have a transaction of 2,500 zaap in your desktop Cold Wallet.

To carry this out you need to open your zaap Core wallet.

Now you will have to go to the "File" tab and select "Receiving Addresses...".

Click on the "New" button and write a label for your new address (for example, "mn01").

Right click on the address you just created and click on "Copy Address" and click on the "Close" button.

Now that you have the address created, you will send 2,500 zaap to your address. With this, you will have a transaction of 2,500 zaap in your wallet.

Click on the "Send" tab and on "Pay To", enter the address you just created. You will see that in "Label" the name that you put before will appear.

Then you must enter the value of "1000" in "Amount" and you must NOT select "Substract fee from amount". Finally, you must click on the "Send" button.

In the "Overview" tab, a "Payment to yourself" will appear. This transaction must be confirmed by the network, so you will have to wait for it to be confirmed (it may take five minutes of waiting depending on the state of the network and the commission chosen).

## Cold Wallet Setup Part 1

### Enabling zaap Core wallet advanced options

Open your zaap Core wallet on your desktop and now click on "Settings" tab, press on "Options..." and select "Wallet" tab.

The options that you have to select are the following:
* "Enable coin control features".
* "Show Masternodes Tab".

In the image you will see the two options that you will have to select.

![Options on Wallet Settings to select](https://github.com/zaapnetwork/zaap-doc-master/blob/master/masternode-setup/zaapwalletsettings.png "Wallet Settings")

Press "OK" button. In order to enable the new options, you have to restart zaap Core wallet.

### Creating Masternode private key

Open your zaap Core wallet on your desktop and now click on "Tools" tab and press on "Debug console".

Now you have to enter the following command:

```
masternode genkey
```

You should see a long key that looks like:
```
2lmrmsejjknrjg28q9dneiejgggsehgageuufj623y
```

This is your Masternode `private key`. Keep it safe and do not share with anyone.

## VPS Setup

### VPS Connection

Depending on your local machine, you will need to use different steps:
* Windows users: You have to connect using PuTTY and it is recommended to [follow this guide](https://www.digitalocean.com/community/tutorials/how-to-log-into-your-droplet-with-putty-for-windows-users).
* Linux users: You have to connect using ssh.

### Masternode Installation

Copy and paste these commands into your VPS and hit enter. Remember that one box at a time:

```
apt-get -y update
```

```
apt-get -y upgrade
```

### Secure your VPS

Install Fail2ban:

```
apt-get -y install fail2ban
```

```
service fail2ban restart
```

### Install dependencies and zaap Daemon

```
apt-get -y install software-properties-common
```

```
apt-add-repository -y ppa:bitcoin/bitcoin
```

```
apt-get -y update
```

```
apt-get -y install \
    wget \
    git \
    unzip \
    libevent-dev \
    libboost-dev \
    libboost-chrono-dev \
    libboost-filesystem-dev \
    libboost-program-options-dev \
    libboost-system-dev \
    libboost-test-dev \
    libboost-thread-dev \
    libminiupnpc-dev \
    build-essential \
    libtool \
    autotools-dev \
    automake \
    pkg-config \
    libssl-dev \
    libevent-dev \
    bsdmainutils \
    libzmq3-dev \
    nano
```

```
apt-get -y update
```

```
apt-get -y upgrade
```

```
apt-get -y install libdb4.8-dev
```

```
apt-get -y install libdb4.8++-dev
```

```
wget https://github.com/zaapnetwork/zaap/releases/download/v1.0.0/zaapcore-1.0.0-linux64.tar.gz
```

```
tar -xvf zaapcore-1.0.0-linux64.tar.gz
```

```
rm zaapcore-1.0.0-linux64.tar.gz
```

```
cp zaapcore-1.0.0/bin/zaap{d,-cli} /usr/local/bin
```
```
cd
```
```
mkdir -p .zaapcore
```

```
nano .zaapcore/zaap.conf
```

Replace:

```
externalip=VPS_IP_ADDRESS
masternodeprivkey=WALLET_GENKEY
```

With your info!

```
rpcuser=randuserusertext
rpcpassword=passwordrandomtext
rpcallowip=127.0.0.1
listen=1
server=1
daemon=0
logtimestamps=1
maxconnections=256
externalip=VPS_IP_ADDRESS
masternodeprivkey=WALLET_GENKEY
masternode=1


CTRL X to save it. Y for yes, then ENTER.

### Create zaap Service

Now you have to create zaapd Service:


```
nano /etc/systemd/system/zaapd.service

```

With following info:
```
[Unit]
Description=zaapd
After=network.target
[Service]
Type=simple
User=root
WorkingDirectory=/root/
ExecStart=/usr/local/bin/zaapd -conf=/root/.zaapcore/zaap.conf -datadir=/root/.zaapcore
ExecStop=/usr/local/bin/zaap-cli -conf=/root/.zaapcore/zaap.conf -datadir=/root/.zaapcore stop
Restart=on-abort
[Install]
WantedBy=multi-user.target
```

CTRL X to save it. Y for yes, then ENTER.

```
systemctl enable zaapd
```

```
systemctl start zaapd
```

#### Installing Sentinel (No need on v1.0.0)

```
apt-get -y install virtualenv python-pip
```

```
git clone https://github.com/zaapnetwork/sentinel /sentinel
```

```
cd /sentinel
```

```
virtualenv venv
```

```
. venv/bin/activate
```

```
pip install -r requirements.txt
```

```
crontab -e
```

Hit 2. This will bring up an editor. Paste the following in it at the bottom.

```
* * * * * cd /sentinel && ./venv/bin/python bin/sentinel.py >/dev/null 2>&1
```

CTRL X to save it. Y for yes, then ENTER.

Use `watch zaap-cli getinfo` to check and wait until it's synced (look for blocks number and compare with a [block explorer](http://explorer.zaapnetwork.io/)).

## Cold Wallet Setup Part 2

1. On your local machine open your `masternode.conf` file.
   Depending on your operating system you will find it in:
   * Windows: `%APPDATA%\zaapCore\`
   * Mac OS: `~/Library/Application Support/zaapCore/`
   * Unix/Linux: `~/.zaapcore/`

   Leave the file open
2. Go to "Tools" -> "Debug console"
3. Run the following command: `masternode outputs`
4. You should see output like the following if you have a transaction with exactly 2500 zaap:
```
{
    "12345678xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx": "0"
}
```
5. The value on the left is your `txid` and the right is the `vout`.
6. Add a line to the bottom of the already opened `masternode.conf` file using the IP of your VPS (with port 25193), `private key`, `txid` and `vout`:
```
mn1 1.2.3.4:24126 3xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 12345678xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 0
```
7. Save the file, exit your wallet and reopen your wallet.
8. Go to the "Masternodes" tab.
9. Click "Start All".
10. You will see "WATCHDOG_EXPIRED". Just wait few minutes.

Congratulations! You have just owned a zaap Master Node. A master node helps improve the network and provide the network with certain anonymous and fast payments functions, also including governance functions.

## How can I check my masternode is working?

Assuming you are using the hot/cold wallet as recommended in our guides, check the following:

1. On Windows/Mac wallet masternode tab
The status field is ENABLED and the Active field is greater than 0:00.

2. In Windows/Mac wallet debug console
Type `masternode list-conf` and ensure everything looks correct, particularly that "status" : "ENABLED".

3. On linux vps (via PuTTY)
Type `zaap-cli masternode status` and look for "message" : "Masternode successfully started".

If all these checks are successful, your masternode is working correctly. You may have to wait over 40 hours for your FIRST reward.

### Future improvements

* Create masternode user (move zaap files to user home).
* Masternode user should launch zaap service and cron job (and not root user).
* Create swap to improve OS performance.
* Increase security with UFW (Uncomplicated Firewall).
