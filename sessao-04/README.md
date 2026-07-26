# Sessão 4 - Gestão Segura de Acessos Remotos SSH em Linux

**Módulo:** Linux e Cibersegurança  
**Objetivo:** OA4. Aplicar  

---

## 1. Configuração do Daemon SSH (`sshd_config`)

```text
Port 2222
PermitRootLogin no
PasswordAuthentication no
```

---

## 2. Evidência de Execução Completa no Terminal

```bash
root@ip-10-128-147-41:~# sudo adduser teste
fatal: The user `teste' already exists.
root@ip-10-128-147-41:~# sudo adduser teste1
info: Adding user `teste1' ...
info: Selecting UID/GID from range 1000 to 59999 ...
info: Adding new group `teste1' (1003) ...
info: Adding new user `teste1' (1003) with group `teste1 (1003)' ...
info: Creating home directory `/home/teste1' ...
info: Copying files from `/etc/skel' ...
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: password updated successfully
Changing the user information for teste1
Enter the new value, or press ENTER for the default
	Full Name []: 
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
info: Adding new user `teste1' to supplemental / extra groups `users' ...
info: Adding user `teste1' to group `users' ...
root@ip-10-128-147-41:~# ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/root/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_ed25519
Your public key has been saved in /root/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:8mA1n4teS66yN2U3hlXzrJYEO55AAYF+riyM8AR06sE root@ip-10-128-147-41
The key's randomart image is:
+--[ED25519 256]--+
|       .oo..     |
| . .  .   . . o  |
|o o  .  o.   + + |
|.E    ...o..+ . o|
|...    +oS o= + o|
|...   . +..+.* + |
| + o . .oo+o o   |
|  o o +.o+ .     |
|      ..+ooo     |
+----[SHA256]-----+
root@ip-10-128-147-41:~# ssh-copy-id teste1@127.0.0.1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_ed25519.pub"
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
ED25519 key fingerprint is SHA256:dYYsjw+W+/JgKETJurMfj+1LMnYcv0KQvcAqcE3raG4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
teste1@127.0.0.1's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'teste1@127.0.0.1'"
and check to make sure that only the key(s) you wanted were added.

root@ip-10-128-147-41:~# nano /etc/ssh/sshd_config
root@ip-10-128-147-41:~# sshd -t
root@ip-10-128-147-41:~# system restart sshd
Command 'system' not found, did you mean:
  command 'systemd' from deb systemd (255.4-1ubuntu8.16)
  command 'system3' from deb simh (3.8.1-6.1)
Try: apt install <deb name>
root@ip-10-128-147-41:~# systemctl restart sshd
Failed to restart sshd.service: Unit sshd.service not found.
root@ip-10-128-147-41:~# systemctl status sshd
Unit sshd.service could not be found.
root@ip-10-128-147-41:~# systemctl restart ssh
root@ip-10-128-147-41:~# ssh -i ~/.ssh/id_ed255519 -p 2222 teste@127.0.0.1
Warning: Identity file /root/.ssh/id_ed255519 not accessible: No such file or directory.
ssh: connect to host 127.0.0.1 port 2222: Connection refused
root@ip-10-128-147-41:~# nano /etc/ssh/sshd_config
root@ip-10-128-147-41:~# systemctl daemon-reload
root@ip-10-128-147-41:~# systemctl restart ssh.socket
root@ip-10-128-147-41:~# ssh -i ~/.ssh/ed25519 -p 2222 teste1@127.0.0.1
Warning: Identity file /root/.ssh/ed25519 not accessible: No such file or directory.
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.17.0-1012-aws x86_64)

 * Documentation:  [https://help.ubuntu.com](https://help.ubuntu.com)
 * Management:     [https://landscape.canonical.com](https://landscape.canonical.com)
 * Support:        [https://ubuntu.com/pro](https://ubuntu.com/pro)

  System information as of Sun Jul 26 18:56:09 UTC 2026

  System load:  0.04                Temperature:           -273.1 C
  Usage of /:   82.4% of 28.02GB   Processes:             213
  Memory usage: 57%                Users logged in:       0
  Swap usage:   0%                 IPv4 address for ens5: 10.128.147.41

 * Ubuntu Pro delivers the most comprehensive open source security and
   compliance features.

   [https://ubuntu.com/aws/pro](https://ubuntu.com/aws/pro)

Expanded Security Maintenance for Applications is not enabled.

13 updates can be applied immediately.
11 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

83 additional security updates can be applied with ESM Apps.
Learn more about enabling ESM Apps service at [https://ubuntu.com/esm](https://ubuntu.com/esm)


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
```

