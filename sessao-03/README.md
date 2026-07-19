# Hardening de Redes Linux e Configuração de Firewalls

## 1. Regras do UFW Ativas (`sudo ufw status verbose`)

```text
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)
```
## 2. Listagem Detalhada de Regras do iptables (`sudo iptables -L -v`)

```text
Chain INPUT (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DROP       all  --  any    any     203.0.113.50         anywhere            
36290   22M ufw-before-logging-input  all  --  any    any     anywhere             anywhere            
36290   22M ufw-before-input  all  --  any    any     anywhere             anywhere            
   11  1248 ufw-after-input  all  --  any    any     anywhere             anywhere            
   11  1248 ufw-after-logging-input  all  --  any    any     anywhere             anywhere            
   11  1248 ufw-reject-input  all  --  any    any     anywhere             anywhere            
   11  1248 ufw-track-input  all  --  any    any     anywhere             anywhere            

Chain FORWARD (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 ufw-before-logging-forward  all  --  any    any     anywhere             anywhere            
    0     0 ufw-before-forward  all  --  any    any     anywhere             anywhere            
    0     0 ufw-after-forward  all  --  any    any     anywhere             anywhere            
    0     0 ufw-after-logging-forward  all  --  any    any     anywhere             anywhere            
    0     0 ufw-reject-forward  all  --  any    any     anywhere             anywhere            
    0     0 ufw-track-forward  all  --  any    any     anywhere             anywhere            

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
35717   32M ufw-before-logging-output  all  --  any    any     anywhere             anywhere            
35717   32M ufw-before-output  all  --  any    any     anywhere             anywhere            
  370 32435 ufw-after-output  all  --  any    any     anywhere             anywhere            
  370 32435 ufw-after-logging-output  all  --  any    any     anywhere             anywhere            
  370 32435 ufw-reject-output  all  --  any    any     anywhere             anywhere            
  370 32435 ufw-track-output  all  --  any    any     anywhere             anywhere            

Chain ufw-user-input (1 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 ACCEPT     tcp  --  any    any     anywhere             anywhere             tcp dpt:ssh
```
## 3. Explicação da Política Aplicada

* **Política Padrão Restritiva (Whitelisting):** Configuração de uma postura defensiva estrita onde o tráfego de entrada não autorizado é bloqueado por omissão (`Default: deny (incoming)`), reduzindo a superfície de ataque do servidor. O tráfego de acesso de saída está livre (`Default: allow outgoing`).
* **Acesso SSH Legítimo:** A porta `22/tcp` (serviço SSH) foi explicitamente permitida na cadeia `ufw-user-input` para garantir a gerência remota segura do sistema.
* **Bloqueio Imediato de IP Malicioso:** Foi inserida uma regra manual no topo da chain `INPUT` do iptables para descartar (`DROP`) imediatamente pacotes vindos do IP `203.0.113.50`, neutralizando qualquer tentativa de interação vinda dessa origem específica antes mesmo de passar pelas regras gerais do UFW.
* **Persistência de Regras:** A estrutura foi guardada com sucesso para que as configurações permaneçam ativas automaticamente após qualquer reinicialização do sistema.
