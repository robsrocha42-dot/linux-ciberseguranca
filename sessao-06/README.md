# Laboratório Sessão 6 - Relatório Técnico Defensivo Linux

## 1. Identificação
Análise inicial do sistema revelou múltiplos serviços e portas em escuta sem necessidade operacional, além de configurações permissivas no serviço SSH.

---

## 2. Contenção
Aplicação de regras restritivas no firewall local para impedir acessos externos não autorizados aos serviços expostos.

---

## 3. Remediação
- Hardening do serviço SSH com remoção de chaves não autorizadas e ajuste das configurações em `sshd_config`.
- Bloqueio de portas de alto risco no UFW, mantendo apenas o tráfego essencial (22/TCP).

---

## 4. Validação
- Confirmação das regras ativas do UFW via `ufw status verbose` (evidência no arquivo `ufw-rules.txt`).
- Auditoria e verificação de enrijecimento do sistema.
