---

title: "Installing tp-link Archer T2U plus WiFi adapter drivers on Ubuntu 22.04"

date: 2022-09-02T19:42:38+05:00

draft: false

---

# Installing tp-link Archer T2U plus WiFi adapter drivers on Ubuntu 22.04

So recently I bought a tp link Archer T2U plus wifi adapter and install it drivers on my Ubuntu 20.04 laptop. Here are the installation steps I went through:

  

1\. Check USB connection  

 

```
>> lsusb

Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 004: ID 04f2:b735 Chicony Electronics Co., Ltd HP Wide Vision HD Camera
Bus 003 Device 003: ID 046d:c542 Logitech, Inc. Wireless Receiver
Bus 003 Device 005: ID 8087:0026 Intel Corp. AX201 Bluetooth
Bus 003 Device 011: ID 2357:0120 TP-Link Archer T2U PLUS [RTL8821AU]
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

```

  

2\. Installing the update and drivers  

  

> `sudo apt update`
> 
> `sudo apt install dkms git   ` sudo apt install build-essential libelf-dev linux-headers-$(uname -r)

  
3\. Download the Driver files using `git` :  

> `git clone https://github.com/aircrack-ng/rtl8812au.git`

Navigate to the Downloaded directory :  

> `cd rtl88*`

Install the Driver  

> `sudo make dkms_install`

or

> `# Build the Code`    
> `make`    
> `   ``# Need Admin Permissions to Start Install`    
> `sudo make install` 

  

5\. Restarting the network manager

```
sudo service NetworkManager restart

```

  

  

These steps will help you get your wifi adapter up and running.
