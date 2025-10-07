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

| Ferramenta | Uso no Projeto | Badge/Logo |
| :---: | :--- | :---: |
| **VirtualBox** | Gerenciamento da rede Host-Only e das VMs. | ![VirtualBox](https://img.shields.io/badge/VirtualBox-181717?style=for-the-badge&logo=virtualbox&logoColor=orange) |
| **Kali Linux** | Sistema operacional do atacante. | ![Kali Linux](https://img.shields.io/badge/Kali_Linux-557C94?style=for-the-badge&logo=kali-linux&logoColor=white) |
| **Metasploitable 2** | Alvo vulnerável (FTP, SMB). | <img src="images/logo_metasploitable.png" alt="Metasploitable Logo" width="80"/> |
| **Medusa** | Ataques paralelos, como Password Spraying em SMB. | ![Medusa](https://img.shields.io/badge/Medusa-8E44AD?style=for-the-badge&logo=hackaday&logoColor=white) |
| **Hydra** | Ataques de login em diversos protocolos (FTP, Web). | ![Hydra](https://img.shields.io/badge/Hydra-4A235A?style=for-the-badge&logo=python&logoColor=white) |
| **DVWA** | Alvo para testes de formulários Web. | <img src="images/logo_dvwa.png" alt="DVWA Logo" width="80"/> |

***(Nota: Para as ferramentas sem badge oficial, usamos ícones temáticos ou pedimos para você colocar a imagem do logo na pasta `images/`)***

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
