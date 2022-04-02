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










