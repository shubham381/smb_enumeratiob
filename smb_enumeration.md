# SMB (SERVER MESSAGE BLOCK)
**SMB** stands for server message block. It’s a protocol for sharing resources like files, printers, in general any resource which should be retreivable or made available by the server. It primarily runs on port **445** or port **139** depending on the server . It is actually natively available in windows, so windows users don’t need to configure anything extra as such besides basic setting up. In linux however ,it is a little different. To make it work for linux, you need to install a samba server because linux natively does not use SMB protocol.

# How does the SMB protocol work?

The SMB protocol enables an application -- or the user of an application -- to access files on a remote server, as well as other resources, including printers, mail slots and [named pipes](https://whatis.techtarget.com/definition/named-pipe). Thus, a client application can open, read, move, create and update files on the remote server. It can also communicate with any server program that is set up to receive an SMB client request.

The SMB protocol is known as a response-request protocol, meaning that it transmits multiple messages between the client and server to establish a connection.

An early dialect of the SMB protocol, Common Internet File System ([CIFS](https://searchstorage.techtarget.com/definition/Common-Internet-File-System-CIFS)), gained notoriety as a [chatty protocol](https://searchnetworking.techtarget.com/definition/chatty-protocol) that bogged down wide area network ([WAN](https://searchnetworking.techtarget.com/definition/WAN-wide-area-network)) performance due to the combined burdens of [latency](https://whatis.techtarget.com/definition/latency) and CIFS' numerous acknowledgments. The next dialect, SMB 2.0, improved the protocol's efficiency by drastically reducing its hundreds of commands and subcommands down to just 19.

The SMB [protocol](https://searchnetworking.techtarget.com/definition/protocol) operates in Layer 7, also known as the application layer, and can be used over [TCP/IP](https://searchnetworking.techtarget.com/definition/TCP-IP) on port 445 for transport. Early dialects of the SMB protocol use the application programming interface (API) [NetBIOS](https://searchnetworking.techtarget.com/definition/NetBIOS) over TCP/IP, or legacy protocols such as the [Internetwork Packet Exchange](https://searchnetworking.techtarget.com/definition/IPX) or [NetBEUI](https://searchwindowsserver.techtarget.com/definition/NetBEUI-NetBIOS-Extended-User-Interface). Today, communication with devices that do not support SMB directly over TCP/IP requires the use of NetBIOS over a transport protocol, such as TCP/IP.

![](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/networking-smb.jpg)

Microsoft Windows operating systems since Windows 95 have included client and server SMB protocol support. [Samba](https://whatis.techtarget.com/definition/Samba), an open source server that supports the SMB protocol, was released for [Unix](https://searchdatacenter.techtarget.com/definition/Unix) systems.

A client and server may implement different variations of SMB, which they negotiate before starting a session.


# SMB enumeration

If you are testing a Windows environment, the easiest way to collect information about that environment is by using the **Server Message Block** (**SMB**) enumeration tool such as `nbtscan`.

The `nbtscan` tool can be used to scan the IP addresses for the NetBIOS name information. It will produce a report that contains the IP address, NetBIOS computer name, services available, logged in username, and MAC addresses of the corresponding machines.This is a command utility that tries to scan NetBIOS name servers open on a local or remote TCP/IP network and because it is a first step in finding open shares. It is created on the functionality of the Windows standard tool ``nbtstat``, and it works on a whole subnet instead of individual IP.

This information will be useful in the penetration testing steps. The difference between `nbtstat` and `nbtscan` of Windows is that `nbtscan` can operate on a range of IP addresses. You should be aware that using this tool will generate a lot of traffic, and it may be logged by the target machines.
``nbtscan 172.16.184./24``

![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/nbtscan.png)

# Enumeration of Services Using NMAP
As SMB runs on 139 or 445,We use **Nmap** scripting engine or commonly known as NSE.
NSE Contains many Scripts which can be used for enumeration and exploitation.We can check the scripts using command

``ls /usr/share/nmap/scripts/ | grep smb``

![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/smb-nmap.png)

or we can use deafult **NSE** script

``nmap -sC -p 139,445 -sV <target ip>``
![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/smb-services.png)

Nice ! we found out the version of Samba running in the server. Now we need to enumerate more about the users and groups so will use metasploit
# Enumeration of Services Using METASPLOIT(msf)
There are many ways to enumerate the service itself.I am using **metasploit**.

After finding the version we can use metasploit to enumerate the service.

``msf5 auxiliary> use auxiliary/scanner/smb/smb_enum``

![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/smb.png)

We can enumerate all this services lets try to enumerate users

``msf5 auxiliary > use auxiliary/scanner/smb/smb_enumusers``

now we need to find requirement 

```msf5 auxiliary(scanner/smb/smb_enumusers) > use auxiliary/scanner/smb/smb_enumusers
msf5 auxiliary(scanner/smb/smb_enumusers) > show options

Module options (auxiliary/scanner/smb/smb_enumusers):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   DB_ALL_USERS  false            no        Add all enumerated usernames to the database
   RHOSTS           yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   SMBDomain     .                no        The Windows domain to use for authentication
   SMBPass                        no        The password for the specified username
   SMBUser                        no        The username to authenticate as
   THREADS       1                yes       The number of concurrent threads (max one per host)
```
Ok! we only need to give the ip of remote host and the it will exploit it automatically..![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/smb_enum.png)
Here we successfully enumerate every user.

The **smb_enumshares** module, as would be expected, enumerates any SMB shares that are available on a remote system.

The **smb_enumusers** scanner will connect to each system via the SMB RPC service and enumerate the users on the system.

The **smb_login** module can also be passed a username and password list in order to attempt to brute-force login attempts across a range of machines.

The **smb_version** scanner connects to each workstation in a given range of hosts and determines the version of the SMB service that is running.


