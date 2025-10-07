<div align="center">
  
# 🛡️ Desafio DIO: Simulação de Ataque de Força Bruta

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Metodologia](https://img.shields.io/badge/Metodologia-Ethical%20Hacking-blue)
![Nível](https://img.shields.io/badge/Nível-Iniciante%20/%20Intermediário-yellow)

</div>

---

## 📋 Sumário
* [🎯 Descrição do Projeto](#-descrição-do-projeto)
* [🛠️ Tecnologias e Ferramentas](#%EF%B8%8F-tecnologias-e-ferramentas)
* [⚙️ Configuração do Ambiente](#%E2%9A%99%EF%B8%8F-configuração-do-ambiente)
* [🚀 Cenários de Ataque Documentados](#-cenários-de-ataque-documentados)
* [💡 Medidas de Mitigação](#-medidas-de-mitigação)
* [🔗 Como Contribuir / Contato](#-como-contribuir--contato)

---

## 🎯 Descrição do Projeto

Este repositório documenta a execução do **Desafio de Projeto da DIO** focado em segurança cibernética. O objetivo foi simular, em um ambiente controlado, cenários de ataque de **Força Bruta** (Brute Force Attack) em diferentes serviços, utilizando ferramentas do Kali Linux para exercitar a compreensão de vulnerabilidades e propor medidas de prevenção eficazes.

> ⚠️ **Disclaimer:** Este projeto foi realizado estritamente em um ambiente de laboratório isolado, utilizando máquinas virtuais propositalmente vulneráveis (Metasploitable 2 e DVWA), com o único propósito de aprendizado e auditoria de segurança.

## 🛠️ Tecnologias e Ferramentas

| Ferramenta | Uso no Projeto |
| :---: | :---: |
| <img src="images/vb.svg" alt="Logo VirtualBox" width="100"/><br/> | Gerenciamento da rede Host-Only e das VMs.
| <img src="images/kali.svg" alt="Logo Kali Linux" width="100"/><br/> | Sistema operacional do atacante.
| <img src="images/metasploitable.png" alt="Logo Metasploitable" width="100"/><br/> | Alvo vulnerável (FTP, SMB).
| <img src="images/medusa.svg" alt="Logo Medusa" width="100"/><br/> | Ataques paralelos, como Password Spraying em SMB.
| <img src="images/hydra.svg" alt="Logo Hydra" width="100"/><br/> | Ataques de login em diversos protocolos (FTP, Web).
| <img src="images/dvwa.png" alt="Logo DVWA" width="100"/><br/> | Alvo para testes de formulários Web.

## ⚙️ Detalhes da Instalação e Versões

Embora muitas das ferramentas (Hydra, Medusa, Nmap) já venham pré-instaladas e configuradas no Kali Linux, é importante listar o comando de instalação (caso necessário) e a versão utilizada.

Observação: Todos os comandos de instalação abaixo foram executados no terminal do Kali Linux.

| Ferramenta | Link | Versão Utilizada
| :---: | :---: | :---: |
| VirtualBox	| https://download.virtualbox.org/virtualbox/7.2.2/VirtualBox-7.2.2-170484-Win.exe |	7.2.2
| Kali Linux	| https://elmirror.cl/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso | Kali Linux 2025.3
| Metasploitable 2	| https://sourceforge.net/projects/metasploitable/ |	2.0.0
| Hydra	| sudo apt install hydra (Se não estiver instalado)	hydra -V | 9.5
| Medusa	| sudo apt install medusa (Se não estiver instalado)	medusa -V | 2.3
| Nmap | (Para Enumeração)	sudo apt install nmap -V | 7.95

## ⚙️ Configuração do Ambiente

A configuração de rede entre as duas Máquinas Virtuais (VMs) é o ponto mais crucial. Optamos pela rede **Host-Only** (Rede Interna/Somente Host) para garantir que o ambiente de ataque estivesse completamente isolado da sua rede doméstica e da Internet.

### 1. Preparação da Rede Host-Only no VirtualBox

1.  **Abrir Preferências:** No VirtualBox, vá em `Arquivo` (File) -> `Preferências` (Preferences) ou pressione `Ctrl + G`.
2.  **Rede:** Selecione `Rede` (Network) e depois `Redes Somente Host` (Host-only Networks).
3.  **Adicionar:** Clique no ícone de **`+`** (Adicionar) para criar uma nova rede Host-Only (Ex: `vboxnet0`).
4.  **Configurar Endereço:** Clique com o botão direito na rede recém-criada e escolha `Propriedades`.
    * **Adaptador:** Defina o IPv4 do adaptador para `192.168.56.1` (Este será o IP do seu Host na rede).
    * **DHCP:** Desabilite o servidor DHCP (`Não`) para gerenciar os IPs manualmente (ou mantenha ativado se preferir, mas desabilitar é mais seguro para este laboratório).

### 2. Configuração das Máquinas Virtuais (VMs)

Para ambas as VMs (Kali Linux e Metasploitable 2):

1.  **Configurações:** Clique na VM e vá em `Configurações` (Settings).
2.  **Rede:** Selecione a seção `Rede` (Network).
3.  **Adaptador 1:** Mude o campo `Conectado a:` para **"Rede Somente Host"** (`Host-only Adapter`).
4.  **Nome:** Selecione a rede Host-Only que você criou no passo 1 (Ex: `vboxnet0`).
5.  **Inicialização:** Inicie ambas as VMs.

### 3. Validação dos Endereços IP

Após iniciar as VMs, é necessário verificar ou definir seus IPs manualmente para garantir a comunicação:

#### A) Kali Linux (Atacante)
* **Comando:** Abra o terminal e execute: `ip addr show` ou `ifconfig`
* **Verificação:** Procure o IP associado ao adaptador `eth1` ou `enp0s8` (o nome do adaptador Host-Only).
* **IP Esperado:** Deve ser algo como `192.168.56.X` (Ex: `192.168.56.101`).

#### B) Metasploitable 2 (Alvo)
* **Comando:** Faça login e execute: `ifconfig`
* **Verificação:** Verifique o IP.
* **IP Esperado:** Deve ser algo como `192.168.56.Y` (Ex: `192.168.56.102`).

### 4. Teste de Conectividade
* Do Kali, teste a comunicação com o Metasploitable:
    ```bash
    ping 192.168.56.102 
    ```
* **Resultado Esperado:** Pacotes de resposta (`64 bytes from...`). Se o ping funcionar, seu ambiente está pronto para os ataques!

## 🚀 Cenários de Ataque Documentados

Aqui detalhamos a execução dos ataques e os comandos utilizados. As evidências (screenshots e GIFs) estão disponíveis na pasta `images/`.

### 1. Força Bruta em Serviço FTP

| Detalhe | Valor |
| :--- | :--- |
| **Alvo** | Metasploitable 2 (Serviço FTP - Porta 21) |
| **Ferramenta** | **Hydra** |
| **Wordlist** | `wordlists/ftp_passwords.txt` |

<div align="center">
    <img src="images/ftp_attack_gif.gif" alt="GIF demonstrando ataque FTP com Hydra" width="600"/>
    <p><i>(Substituir pela sua GIF ou screenshot)</i></p>
</div>

**Comando Utilizado:**

```bash
# Exemplo de comando que utilizou um usuário (-l) e uma lista de senhas (-P)
hydra -l msfadmin -P wordlists/pass_simples.txt ftp://[IP_METASPLOITABLE] -V
