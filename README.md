# Desafio_Cibersegurança

# 📘 Análise Educacional – Criptografia e Captura de Teclas em Python

Este repositório contém **quatro códigos em Python** analisados exclusivamente para fins **educacionais**, demonstrando como softwares maliciosos funcionam em ambientes controlados e como **proteger-se** deles.

> ⚠️ **Atenção:**
> Os códigos abordam técnicas utilizadas em ataques reais (ransomware e keyloggers).
> O objetivo deste repositório é **aprender o funcionamento destas técnicas**.
> Usado somente em ambientes isolados e com finalidade de estudo.

---

## 📂 Conteúdo

* [ransomware.py](#ransomwarepy--criptografia-de-arquivos-simulação-de-ransomware)
* [descriptografar.py](#descriptografarpy--recuperação-de-arquivos-criptografados)
* [keylogger.pyw](#keyloggerpyw--keylogger-local)
* [keylogger_email.py](#keylogger_emailpy--keylogger-com-envio-por-e-mail)
* [Dicas de Segurança](#dicas-de-segurança)

---

# ransomware.py – Criptografia de Arquivos (Simulação de Ransomware)

Este script demonstra como um ransomware pode:

* Gerar uma chave de criptografia (Fernet).
* Criptografar diversos arquivos dentro de um diretório.
* Criar uma mensagem de “resgate”.
* Sobrescrever arquivos originais.

### Conceitos estudados

* Criptografia simétrica com `cryptography.fernet`
* Varredura de diretórios com `os.walk`
* Manipulação de arquivos binários

---

# descriptografar.py – Recuperação de Arquivos Criptografados

Complementa o ransomware.py, realizando:

* Leitura da chave previamente gerada.
* Busca dos arquivos criptografados.
* Descriptografia do conteúdo.

### Conceitos estudados

* Reversão de criptografia simétrica
* Gerenciamento de arquivos criptografados

---

# keylogger.pyw – Keylogger Local

Captura teclas digitadas e registra em `log.txt`.

### Conceitos estudados

* Monitoramento de teclado com `pynput`
* Registro contínuo de teclas
* Tratamento de teclas especiais
* Execução de códigos em segundo plano

---

# keylogger_email.py – Keylogger com Envio por E-mail

Captura teclas e envia periodicamente o registro para um e-mail configurado.

### Conceitos estudados

* Interceptação de teclado
* Exfiltração de dados via SMTP
* Agendamento periódico com `Timer`

---

# Dicas de Segurança

Esta seção explica, de forma clara e acessível, como se proteger contra ameaças parecidas com as que aparecem nos códigos estudados — como **ransomware** e **keyloggers**.

---

## 🔐 1. Como se proteger de ransomware

**Ransomware** é um tipo de ataque que criptografa arquivos e depois exige pagamento para devolvê-los.
Para evitar este tipo de problema:

### ✔️ Faça backups regularmente

* Mantenha uma cópia dos seus arquivos **em um HD externo** ou **na nuvem**.
* Guarde pelo menos um backup **desconectado da internet**.

### ✔️ Desconfie de arquivos desconhecidos

* Não execute **scripts**, programas ou anexos enviados por pessoas desconhecidas.
* Evite baixar arquivos de sites duvidosos.

### ✔️ Mantenha o sistema atualizado

* Atualizações corrigem falhas que podem ser usadas por ransomware.

### ✔️ Use antivírus com proteção comportamental

* Alguns antivírus detectam quando um programa tenta **criptografar muitos arquivos de uma vez**, bloqueando a ameaça.

### ✔️ Proteja suas pastas

* No Windows, ative a proteção **"Controlled Folder Access"** (Proteção de Acesso a Pastas).

---

## 🎹 2. Como se proteger de keyloggers

**Keyloggers** são programas que capturam tudo o que você digita, como senhas, mensagens e dados bancários.

### ✔️ Preste atenção em programas instalados

* Muitos keyloggers chegam escondidos em programas falsos.
* Instale sempre de fontes confiáveis.

### ✔️ Mantenha o antivírus atualizado

* Antivírus modernos detectam comportamentos típicos de keyloggers.

### ✔️ Atualize o sistema e drivers

* Vulnerabilidades antigas podem permitir que softwares monitorem seu teclado.

### ✔️ Cuidado com computadores públicos

* Evite fazer login em contas sensíveis em lan houses, cyber cafés ou máquinas de terceiros.

### ✔️ Use autenticação em duas etapas (2FA)

* Mesmo que alguém capture sua senha, ele ainda não conseguirá acessar sua conta.

---

