# Traffic Analysis Exercise — Easy As 123

Este documento descreve a análise do exercício **“2026-02-28 – Traffic Analysis Exercise: Easy As 123”** disponibilizado pela Malware Traffic Analysis.

O objetivo do laboratório foi analisar um arquivo **PCAP** e identificar qual host da rede interna estava comprometido por um **RAT**.

---

# Contexto

O SIEM gerou alertas indicando possível atividade associada ao malware **NetSupport Manager RAT**.

Informações fornecidas para a investigação:

- **IP externo:** 45.131.214.85  
- **Porta:** 443  
- **Início da atividade:** 28/02/2026 às 19:55 UTC  
- **Rede interna:** 10.2.28.0/24  
- **Domínio:** easyas123.tech  

Foi disponibilizado um **arquivo PCAP** contendo o tráfego capturado da rede para análise.

---

# Objetivo

Identificar os seguintes dados da máquina comprometida:

- IP  
- MAC Address  
- Hostname  
- Username  
- Nome completo do usuário  

---

# Ferramenta utilizada

- Wireshark

---

# Identificação do host suspeito

Inicialmente foi analisada a tabela de conversas IPv4 para identificar hosts com comunicação relevante.

Caminho no Wireshark:


Statistics → Conversations → IPv4


Durante essa análise foi identificado um host interno com comunicação com o IP externo mencionado no alerta.

**IP suspeito:**


10.2.28.88


---

# Identificação do MAC Address

Aplicando o filtro:


ip.addr == 10.2.28.88


e examinando o cabeçalho **Ethernet II**, foi possível identificar o MAC address do host.


00:19:d1:b2:4d:ad


---

# Identificação do hostname

O hostname foi identificado através da análise do protocolo **NBNS (NetBIOS Name Service)**.

Um pacote de registro NetBIOS revelou o nome da máquina associado ao endereço IP.


Hostname: DESKTOP-TEYQ2NR


---

# Identificação do username

A análise do tráfego **Kerberos** permitiu identificar o usuário autenticado no domínio.

No campo **CNameString** foi encontrado o seguinte valor:


Username: bholf


---

# Identificação do nome completo

O nome completo não foi identificado diretamente no tráfego Kerberos.

Durante a análise de consultas **LDAP** no fluxo de autenticação do Active Directory, foi possível correlacionar o username com os atributos completos do usuário.


Nome completo: Becka Holf


---

# Validação da comunicação suspeita

Para validar o comportamento do host, foi aplicado o filtro:


ip.addr == 10.2.28.88 && http


Foi identificado tráfego HTTP destinado ao endereço:


45.131.214.85


Entre os artefatos observados:

- Requisições **POST**
- Endpoint utilizado:


/fakeurl.htm


- **User-Agent**


NetSupport Manager/1.3


As requisições continham dados codificados, comportamento compatível com comunicação de **Command and Control (C2)**.

---

# Resultado

Host comprometido identificado:


IP: 10.2.28.88
MAC Address: 00:19:d1:b2:4d:ad
Hostname: DESKTOP-TEYQ2NR
Username: bholf
Nome completo: Becka Holf


O host apresenta comunicação com o servidor externo **45.131.214.85**, associada ao comportamento do **NetSupport Manager RAT**, indicando comprometimento da máquina.
