# Windows Server network infrastructure

This document is **not suitable for production** but show the basic usage type of a Windows Server infrastructure.

The entire document assume that you know how to setup a Virtual Machine and use the english version of the system.

In graphic manipulation context all manipulation that ivolves graphic manipulation will be highligh then followed by the button or menu text.

## Requirement

- This infrastructure require **at least 2 Windows Server 2016** edition and at least one client of **Windows 7 or higher**;
- The setup process must be followed by a **renitialized with sysprep** if it was cloned from a system template to get a correct SID;
- Ensure that all server are up to date with **Windows Update** before any manipulation;
- Setup a fresh password to the Administrator account;
- **A dedicated subnet is highly recommanded** to avoid a DHCP conflict.

## Defining a new Active Directory Domain Services

In this part we will define a main server that will hold the master role of the cluster and the second server will serve as a typical read only cluster replica.

### Server A

Step 1: Starting from the **Windows Start**.

- **Open**  Server Manager;
- **Click _(Menu)_** Manage;
- **Click** Add Roles and Features;
- **Next _(Until Server Roles)_**;
- **Check** Active Directory Domain Services;
- **Add Features**;
- **Next _(Until Confirm installation selections)_**;
- **Install**;
- **Reboot**.

Step 2: Now a **Warning icon** should had appear next to the **flag**

- **Click** Flag;
- **Click** Promote this server to a domain controller;
- **Select** Add a new forest;
- **Enter** A domain name. (e.g. "universe.local");
- **Enter** a password (e.g. "STAR4ever");
- **Next _(Until Installation)_**;
- **Install**.

### Server B

Step 1: Repeat the step 1 proccedure of the **Server A**.

Step 2: Now a **Warning icon** should had appear next to the **flag**.

- **Click** Flag;
- **Click** Promote this server to a domain controller;
- **Enter** Selected Domain name during **Server A** installation;
- **Change** Login using **Server A** credentials;
- **Next _(Until Installation)_**;
- **Install**.

## Create users and groups

Starting from the **Server Manager**.

- **Clcik** Tools
- **Select** Active Directory Users and Computers

## Configure a DHCP server

Step 1: Starting from the **Server Manager**.

- **Click _(Menu)_** Manage;
- **Click** Add Roles and Features;
- **Next _(Until Server Roles)_**;
- **Check** DHCP Server;
- **Add Features**;
- **Next _(Until Confirm installation selections)_**;
- **Install**;
- **Reboot**.

## Reconfigure Netowrk Time Protocole server

### Server A and B

In this example we will be syncronise with ntp.laas.fr.

Starting from the **Server Manager**.

- **Open** Powershell with admin right

```powershell
PS C:\> w32tm /config /manualpeerlist:ntp.laas.fr /syncfromflags:MANUAL
The command completed successfully.

PS C:\> Restart-Service w32time

PS C:\> w32tm /resync
Sending resync command to local compute
The command completed successfully.

PS C:\> w32tm /query /status
Leap Indicator: 0(no warning)
Stratum: 3 (secondary reference - syncd by (S)NTP)
Precision: -6 (15.625ms per tick)
Root Delay: 0.0258817s
Root Dispersion: 7.7688656s
ReferenceId: 0xC3538487 (source IP:  195.83.132.135)
Last Successful Sync Time: 8/4/2018 10:14:27 PM
Source: ntp.laas.fr
Poll Interval: 6 (64s)
```
