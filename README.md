# Sessão 1: Introdução ao Linux para Segurança e Comandos de Rede

* **Curso:** Reskilling
* **Módulo:** Linux e Cibersegurança
* **Formador:** Péricles Borges
* **Objetivo:** Mapeamento e análise da superfície de exposição de um servidor alvo na rede local.

---

## 1. Análise do Ambiente Local (TryHackMe / AttackBox)

### A. Identificação do Endereço IP da Interface Principal
Comando executado para identificar o IP local:
```bash
ip a     
root@ip-10-128-173-112:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
    link/ether 06:ca:52:3f:7f:65 brd ff:ff:ff:ff:ff:ff
    altname enp0s5
    inet 10.128.173.112/18 metric 100 brd 10.128.191.255 scope global dynamic ens5
       valid_lft 2120sec preferred_lft 2120sec
    inet6 fe80::4ca:52ff:fe3f:7f65/64 scope link 
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 6e:93:99:f6:ed:94 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::6c93:99ff:fef6:ed94/64 scope link 
       valid_lft forever preferred_lft forever
4: veth37dfc89@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether c2:93:84:56:3d:c2 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::c093:84ff:fe56:3dc2/64 scope link 
       valid_lft forever preferred_lft forever
5: vethffbd26b@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether b6:81:a1:35:c2:8d brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::70d1:e7ff:fe4b:996c/64 scope link 
       valid_lft forever preferred_lft forever

ss -tuln

root@ip-10-128-173-112:~# ss -tuln
Netid State   Recv-Q Send-Q       Local Address:Port   Peer Address:Port Process 
udp   UNCONN 0      0                  0.0.0.0:8443        0.0.0.0:*            
udp   UNCONN 0      0                127.0.0.1:323         0.0.0.0:*            
udp   UNCONN 0      0                  0.0.0.0:53          0.0.0.0:*            
udp   UNCONN 0      0      10.128.173.112%ens5:68          0.0.0.0:*            
udp   UNCONN 0      0                127.0.0.1:111         0.0.0.0:*            
udp   UNCONN 0      0           172.17.255.255:137         0.0.0.0:*            
udp   UNCONN 0      0               172.17.0.1:137         0.0.0.0:*            
udp   UNCONN 0      0           10.128.191.255:137         0.0.0.0:*            
udp   UNCONN 0      0           10.128.173.112:137         0.0.0.0:*            
udp   UNCONN 0      0                  0.0.0.0:137         0.0.0.0:*            
udp   UNCONN 0      0           172.17.255.255:138         0.0.0.0:*            
udp   UNCONN 0      0               172.17.0.1:138         0.0.0.0:*            
udp   UNCONN 0      0           10.128.191.255:138         0.0.0.0:*            
udp   UNCONN 0      0           10.128.173.112:138         0.0.0.0:*            
udp   UNCONN 0      0                  0.0.0.0:138         0.0.0.0:*            
udp   UNCONN 0      0                     [::]:8443           [::]:*            
udp   UNCONN 0      0                    [::1]:323            [::]:*            
udp   UNCONN 0      0                     [::]:53             [::]:*            
udp   UNCONN 0      0                    [::1]:111            [::]:*            
tcp   LISTEN 0      50                 0.0.0.0:445         0.0.0.0:*            
tcp   LISTEN 0      4096               0.0.0.0:22          0.0.0.0:*            
tcp   LISTEN 0      32                 0.0.0.0:53          0.0.0.0:*            
tcp   LISTEN 0      50                 0.0.0.0:139         0.0.0.0:*            
tcp   LISTEN 0      10                 0.0.0.0:8443        0.0.0.0:*            
tcp   LISTEN 0      200              127.0.0.1:5433        0.0.0.0:*            
tcp   LISTEN 0      1024             127.0.0.1:20976       0.0.0.0:*            
tcp   LISTEN 0      4096               0.0.0.0:7777        0.0.0.0:*            
tcp   LISTEN 0      4096               0.0.0.0:7778        0.0.0.0:*            
tcp   LISTEN 0      4096             127.0.0.1:111         0.0.0.0:*            
tcp   LISTEN 0      50                    [::]:445            [::]:*            
tcp   LISTEN 0      4096                  [::]:22             [::]:*            
tcp   LISTEN 0      32                    [::]:53             [::]:*            
tcp   LISTEN 0      4096                 [::1]:111            [::]:*            
tcp   LISTEN 0      50                    [::]:139            [::]:*            
tcp   LISTEN 0      10                    [::]:8443           [::]:*            
tcp   LISTEN 0      4096                  [::]:7777           [::]:*            
tcp   LISTEN 0      4096                  [::]:7778           [::]:*

nmap -sV -sC 10.128.161.4

root@ip-10-128-173-112:~# nmap -sV -sC 10.128.161.4
Starting Nmap 7.94SVN ( [https://nmap.org](https://nmap.org) ) at 2026-07-19 18:02 UTC
Nmap scan report for ip-10-128-161-4.eu-west-3.compute.internal (10.128.161.4)
Host is up (0.00026s latency).
Not shown: 995 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           FileZilla ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
135/tcp  open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=win-scan
| Not valid before: 2026-07-18T17:22:06
|_Not valid after:  2027-01-17T17:22:06
| rdp-ntlm-info: 
|   Target_Name: WIN-SCAN
|   NetBIOS_Domain_Name: WIN-SCAN
|   NetBIOS_Computer_Name: WIN-SCAN
|   DNS_Domain_Name: win-scan
|   DNS_Computer_Name: win-scan
|   Product_Version: 10.0.17763
|_  System_Time: 2026-07-19T18:02:53+00:00
|_ssl-date: 2026-07-19T18:03:23+00:00; -1s from scanner time.
MAC Address: 06:19:4B:52:13:65 (Unknown)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at [https://nmap.org/submit/](https://nmap.org/submit/) .
Nmap done: 1 IP address (1 host up) scanned in 46.42 seconds
